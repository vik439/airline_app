# AirLine Application with Django

## Project Structure

```
airline_booking/
├── manage.py
├── airline_booking/
│   ├── __init__.py
│   ├── settings.py
│   ├── urls.py
│   └── wsgi.py
└── bookings/
    ├── __init__.py
    ├── admin.py
    ├── models.py
    └── migrations/
```

## Step 1: Install Django and Create Project

```bash
pip install django
django-admin startproject airline_booking
cd airline_booking
python manage.py startapp bookings
```

## Step 2: Configure Settings

**airline_booking/settings.py:**
```python
INSTALLED_APPS = [
    'django.contrib.admin',
    'django.contrib.auth',
    'django.contrib.contenttypes',
    'django.contrib.sessions',
    'django.contrib.messages',
    'django.contrib.staticfiles',
    'bookings',  # Add our app
]

# Database (using SQLite by default)
DATABASES = {
    'default': {
        'ENGINE': 'django.db.backends.sqlite3',
        'NAME': BASE_DIR / 'db.sqlite3',
    }
}
```

## Step 3: Create Models

**bookings/models.py:**
```python
from django.db import models
from django.core.validators import MinValueValidator, MaxValueValidator
from django.utils import timezone

class Airline(models.Model):
    name = models.CharField(max_length=100)
    code = models.CharField(max_length=3, unique=True, help_text="IATA airline code")
    country = models.CharField(max_length=100)
    founded_date = models.DateField(null=True, blank=True)
    website = models.URLField(blank=True)
    active = models.BooleanField(default=True)
    
    class Meta:
        ordering = ['name']
    
    def __str__(self):
        return f"{self.name} ({self.code})"

class Airport(models.Model):
    name = models.CharField(max_length=200)
    code = models.CharField(max_length=3, unique=True, help_text="IATA airport code")
    city = models.CharField(max_length=100)
    country = models.CharField(max_length=100)
    timezone = models.CharField(max_length=50, default='UTC')
    latitude = models.FloatField(null=True, blank=True)
    longitude = models.FloatField(null=True, blank=True)
    
    class Meta:
        ordering = ['country', 'city']
    
    def __str__(self):
        return f"{self.name} ({self.code}) - {self.city}, {self.country}"

class Aircraft(models.Model):
    AIRCRAFT_TYPES = [
        ('NARROW', 'Narrow-body'),
        ('WIDE', 'Wide-body'),
        ('REGIONAL', 'Regional Jet'),
        ('TURBOPROP', 'Turboprop'),
    ]
    
    manufacturer = models.CharField(max_length=100)
    model = models.CharField(max_length=50)
    registration_number = models.CharField(max_length=20, unique=True)
    capacity_economy = models.IntegerField(validators=[MinValueValidator(1)])
    capacity_business = models.IntegerField(default=0, validators=[MinValueValidator(0)])
    capacity_first = models.IntegerField(default=0, validators=[MinValueValidator(0)])
    aircraft_type = models.CharField(max_length=20, choices=AIRCRAFT_TYPES, default='NARROW')
    airline = models.ForeignKey(Airline, on_delete=models.CASCADE, related_name='aircrafts')
    manufacture_date = models.DateField(null=True, blank=True)
    in_service = models.BooleanField(default=True)
    
    @property
    def total_capacity(self):
        return self.capacity_economy + self.capacity_business + self.capacity_first
    
    def __str__(self):
        return f"{self.manufacturer} {self.model} - {self.registration_number}"

class Flight(models.Model):
    FLIGHT_STATUS = [
        ('SCHEDULED', 'Scheduled'),
        ('DELAYED', 'Delayed'),
        ('BOARDING', 'Boarding'),
        ('DEPARTED', 'Departed'),
        ('ARRIVED', 'Arrived'),
        ('CANCELLED', 'Cancelled'),
        ('DIVERTED', 'Diverted'),
    ]
    
    flight_number = models.CharField(max_length=10, unique=True)
    airline = models.ForeignKey(Airline, on_delete=models.CASCADE, related_name='flights')
    aircraft = models.ForeignKey(Aircraft, on_delete=models.CASCADE, related_name='flights')
    origin = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name='departing_flights')
    destination = models.ForeignKey(Airport, on_delete=models.CASCADE, related_name='arriving_flights')
    departure_time = models.DateTimeField()
    arrival_time = models.DateTimeField()
    duration_minutes = models.IntegerField(validators=[MinValueValidator(1)])
    status = models.CharField(max_length=20, choices=FLIGHT_STATUS, default='SCHEDULED')
    base_price = models.DecimalField(max_digits=10, decimal_places=2)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['departure_time']
    
    def __str__(self):
        return f"{self.flight_number}: {self.origin.code} → {self.destination.code} ({self.departure_time.date()})"

class Passenger(models.Model):
    GENDER_CHOICES = [
        ('M', 'Male'),
        ('F', 'Female'),
        ('O', 'Other'),
        ('N', 'Prefer not to say'),
    ]
    
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    email = models.EmailField(unique=True)
    phone = models.CharField(max_length=20, blank=True)
    date_of_birth = models.DateField()
    gender = models.CharField(max_length=1, choices=GENDER_CHOICES, default='N')
    passport_number = models.CharField(max_length=20, unique=True, blank=True, null=True)
    passport_country = models.CharField(max_length=100, blank=True)
    passport_expiry = models.DateField(null=True, blank=True)
    address = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    
    class Meta:
        ordering = ['last_name', 'first_name']
    
    def __str__(self):
        return f"{self.last_name}, {self.first_name}"

class Booking(models.Model):
    BOOKING_STATUS = [
        ('PENDING', 'Pending'),
        ('CONFIRMED', 'Confirmed'),
        ('CANCELLED', 'Cancelled'),
        ('CHECKED_IN', 'Checked In'),
        ('COMPLETED', 'Completed'),
        ('NO_SHOW', 'No Show'),
    ]
    
    booking_reference = models.CharField(max_length=6, unique=True, help_text="6-character booking reference")
    flight = models.ForeignKey(Flight, on_delete=models.CASCADE, related_name='bookings')
    passenger = models.ForeignKey(Passenger, on_delete=models.CASCADE, related_name='bookings')
    booking_date = models.DateTimeField(default=timezone.now)
    status = models.CharField(max_length=20, choices=BOOKING_STATUS, default='PENDING')
    total_price = models.DecimalField(max_digits=10, decimal_places=2)
    seat_number = models.CharField(max_length=5, blank=True)
    class_type = models.CharField(max_length=20, choices=[
        ('ECONOMY', 'Economy'),
        ('BUSINESS', 'Business'),
        ('FIRST', 'First Class'),
    ], default='ECONOMY')
    special_requests = models.TextField(blank=True)
    created_at = models.DateTimeField(auto_now_add=True)
    updated_at = models.DateTimeField(auto_now=True)
    
    class Meta:
        ordering = ['-booking_date']
        unique_together = ['flight', 'seat_number']  # Prevent double booking same seat
    
    def __str__(self):
        return f"Booking {self.booking_reference} - {self.passenger}"

class Payment(models.Model):
    PAYMENT_METHODS = [
        ('CARD', 'Credit/Debit Card'),
        ('PAYPAL', 'PayPal'),
        ('BANK', 'Bank Transfer'),
        ('CASH', 'Cash'),
    ]
    
    PAYMENT_STATUS = [
        ('PENDING', 'Pending'),
        ('COMPLETED', 'Completed'),
        ('FAILED', 'Failed'),
        ('REFUNDED', 'Refunded'),
    ]
    
    booking = models.OneToOneField(Booking, on_delete=models.CASCADE, related_name='payment')
    amount = models.DecimalField(max_digits=10, decimal_places=2)
    payment_method = models.CharField(max_length=20, choices=PAYMENT_METHODS)
    payment_status = models.CharField(max_length=20, choices=PAYMENT_STATUS, default='PENDING')
    transaction_id = models.CharField(max_length=100, unique=True, blank=True)
    payment_date = models.DateTimeField(default=timezone.now)
    last_four_digits = models.CharField(max_length=4, blank=True)
    
    def __str__(self):
        return f"Payment for {self.booking.booking_reference} - {self.amount}"

class Crew(models.Model):
    CREW_ROLES = [
        ('PILOT', 'Pilot'),
        ('CO_PILOT', 'Co-Pilot'),
        ('FLIGHT_ATTENDANT', 'Flight Attendant'),
        ('PURSER', 'Purser'),
        ('ENGINEER', 'Flight Engineer'),
    ]
    
    first_name = models.CharField(max_length=50)
    last_name = models.CharField(max_length=50)
    employee_id = models.CharField(max_length=20, unique=True)
    role = models.CharField(max_length=20, choices=CREW_ROLES)
    airline = models.ForeignKey(Airline, on_delete=models.CASCADE, related_name='crew_members')
    date_hired = models.DateField()
    languages = models.CharField(max_length=200, blank=True, help_text="Comma-separated languages")
    
    def __str__(self):
        return f"{self.employee_id} - {self.first_name} {self.last_name} ({self.role})"

class FlightCrew(models.Model):
    flight = models.ForeignKey(Flight, on_delete=models.CASCADE, related_name='crew_assignments')
    crew_member = models.ForeignKey(Crew, on_delete=models.CASCADE, related_name='flight_assignments')
    role_on_flight = models.CharField(max_length=20, choices=Crew.CREW_ROLES)
    
    class Meta:
        unique_together = ['flight', 'crew_member']
        verbose_name_plural = "Flight crew assignments"
    
    def __str__(self):
        return f"{self.flight.flight_number} - {self.crew_member}"
```

## Step 4: Create Admin Interface

**bookings/admin.py:**
```python
from django.contrib import admin
from django.utils.html import format_html
from .models import (
    Airline, Airport, Aircraft, Flight, Passenger, 
    Booking, Payment, Crew, FlightCrew
)

@admin.register(Airline)
class AirlineAdmin(admin.ModelAdmin):
    list_display = ['name', 'code', 'country', 'active', 'founded_date']
    list_filter = ['active', 'country']
    search_fields = ['name', 'code', 'country']
    ordering = ['name']
    fieldsets = (
        ('Basic Information', {
            'fields': ('name', 'code', 'country')
        }),
        ('Additional Details', {
            'fields': ('founded_date', 'website', 'active')
        }),
    )

@admin.register(Airport)
class AirportAdmin(admin.ModelAdmin):
    list_display = ['code', 'name', 'city', 'country', 'timezone']
    list_filter = ['country', 'city']
    search_fields = ['name', 'code', 'city', 'country']
    ordering = ['code']

@admin.register(Aircraft)
class AircraftAdmin(admin.ModelAdmin):
    list_display = ['registration_number', 'manufacturer', 'model', 'airline', 
                   'capacity_economy', 'capacity_business', 'capacity_first', 
                   'total_capacity', 'in_service']
    list_filter = ['airline', 'manufacturer', 'aircraft_type', 'in_service']
    search_fields = ['registration_number', 'manufacturer', 'model']
    ordering = ['airline', 'manufacturer', 'model']

@admin.register(Flight)
class FlightAdmin(admin.ModelAdmin):
    list_display = ['flight_number', 'airline', 'origin', 'destination', 
                   'departure_time', 'arrival_time', 'status', 'base_price']
    list_filter = ['airline', 'status', 'origin', 'destination', 'departure_time']
    search_fields = ['flight_number', 'origin__code', 'destination__code']
    ordering = ['-departure_time']
    date_hierarchy = 'departure_time'
    fieldsets = (
        ('Flight Information', {
            'fields': ('flight_number', 'airline', 'aircraft', 'status')
        }),
        ('Route', {
            'fields': ('origin', 'destination', 'departure_time', 'arrival_time', 'duration_minutes')
        }),
        ('Pricing', {
            'fields': ('base_price',)
        }),
    )
    
    def save_model(self, request, obj, form, change):
        # Auto-calculate duration if not provided
        if obj.departure_time and obj.arrival_time:
            duration = obj.arrival_time - obj.departure_time
            obj.duration_minutes = int(duration.total_seconds() / 60)
        super().save_model(request, obj, form, change)

@admin.register(Passenger)
class PassengerAdmin(admin.ModelAdmin):
    list_display = ['last_name', 'first_name', 'email', 'phone', 'passport_number']
    list_filter = ['gender', 'passport_country']
    search_fields = ['first_name', 'last_name', 'email', 'passport_number']
    ordering = ['last_name', 'first_name']
    fieldsets = (
        ('Personal Information', {
            'fields': ('first_name', 'last_name', 'email', 'phone', 'date_of_birth', 'gender')
        }),
        ('Passport Information', {
            'fields': ('passport_number', 'passport_country', 'passport_expiry'),
            'classes': ('collapse',)
        }),
        ('Contact Details', {
            'fields': ('address',),
            'classes': ('collapse',)
        }),
    )

class PaymentInline(admin.StackedInline):
    model = Payment
    extra = 0
    max_num = 1
    fields = ['amount', 'payment_method', 'payment_status', 'transaction_id', 'payment_date']

@admin.register(Booking)
class BookingAdmin(admin.ModelAdmin):
    list_display = ['booking_reference', 'flight', 'passenger', 'booking_date', 
                   'status', 'total_price', 'seat_number']
    list_filter = ['status', 'class_type', 'booking_date']
    search_fields = ['booking_reference', 'passenger__last_name', 'passenger__email']
    ordering = ['-booking_date']
    date_hierarchy = 'booking_date'
    inlines = [PaymentInline]
    fieldsets = (
        ('Booking Reference', {
            'fields': ('booking_reference',)
        }),
        ('Booking Details', {
            'fields': ('flight', 'passenger', 'status', 'class_type')
        }),
        ('Seat Information', {
            'fields': ('seat_number',)
        }),
        ('Pricing', {
            'fields': ('total_price',)
        }),
        ('Additional Information', {
            'fields': ('special_requests',),
            'classes': ('collapse',)
        }),
    )
    
    def save_model(self, request, obj, form, change):
        if not obj.booking_reference:
            # Generate a random 6-character booking reference
            import random
            import string
            obj.booking_reference = ''.join(random.choices(string.ascii_uppercase + string.digits, k=6))
        super().save_model(request, obj, form, change)

@admin.register(Payment)
class PaymentAdmin(admin.ModelAdmin):
    list_display = ['booking', 'amount', 'payment_method', 'payment_status', 
                   'transaction_id', 'payment_date']
    list_filter = ['payment_method', 'payment_status', 'payment_date']
    search_fields = ['booking__booking_reference', 'transaction_id']
    ordering = ['-payment_date']
    readonly_fields = ['payment_date']

class FlightCrewInline(admin.TabularInline):
    model = FlightCrew
    extra = 1

@admin.register(Crew)
class CrewAdmin(admin.ModelAdmin):
    list_display = ['employee_id', 'first_name', 'last_name', 'role', 'airline', 'date_hired']
    list_filter = ['role', 'airline']
    search_fields = ['first_name', 'last_name', 'employee_id']
    ordering = ['last_name', 'first_name']
    inlines = [FlightCrewInline]

@admin.register(FlightCrew)
class FlightCrewAdmin(admin.ModelAdmin):
    list_display = ['flight', 'crew_member', 'role_on_flight']
    list_filter = ['role_on_flight', 'flight__airline']
    search_fields = ['flight__flight_number', 'crew_member__first_name', 'crew_member__last_name']
    ordering = ['flight', 'role_on_flight']
```

## Step 5: Create and Apply Migrations

```bash
python manage.py makemigrations bookings
python manage.py migrate
```

## Step 6: Create Superuser for Admin Access

```bash
python manage.py createsuperuser
```

## Step 7: Run the Development Server

```bash
python manage.py runserver
```

## Step 8: Access the Admin Interface

Open your browser and go to: `http://127.0.0.1:8000/admin/`

## Key Features of This Airline Booking System:

### Models:
1. **Airline** - Airlines operating flights
2. **Airport** - Airports with IATA codes
3. **Aircraft** - Aircraft details with capacity by class
4. **Flight** - Flight schedules and details
5. **Passenger** - Passenger information
6. **Booking** - Flight bookings with unique reference
7. **Payment** - Payment transactions
8. **Crew** - Crew members
9. **FlightCrew** - Crew assignments to flights

### Admin Features:
- Custom list displays with relevant columns
- Search functionality across multiple fields
- Filters for quick data navigation
- Date hierarchy for time-based data
- Inline editing (Payments within Bookings)
- Custom form layouts with fieldsets
- Auto-calculation of flight duration
- Auto-generation of booking references
- Unique constraints to prevent double-booking

## Example Data to Add:

1. Create an Airline: "Delta Air Lines" (DL)
2. Create Airports: "JFK", "LAX", "LHR"
3. Create Aircraft: "Boeing 737-800" with capacity
4. Create Flights: DL123 from JFK to LAX
5. Create Passengers
6. Create Bookings with payments


