

## Python Script to Load Data

Create a file called `load_data.py` in your project root:

**load_data.py:**
```python
import os
import django
import random
import string
from datetime import datetime, timedelta
from decimal import Decimal

os.environ.setdefault('DJANGO_SETTINGS_MODULE', 'airline_booking.settings')
django.setup()

from bookings.models import (
    Airline, Airport, Aircraft, Flight, Passenger, 
    Booking, Payment, Crew, FlightCrew
)

def generate_booking_reference():
    return ''.join(random.choices(string.ascii_uppercase + string.digits, k=6))

def load_data():
    print("Starting data population...")
    
    # Clear existing data (optional)
    # Uncomment if you want to clear data before loading
    # FlightCrew.objects.all().delete()
    # Payment.objects.all().delete()
    # Booking.objects.all().delete()
    # Flight.objects.all().delete()
    # Aircraft.objects.all().delete()
    # Crew.objects.all().delete()
    # Passenger.objects.all().delete()
    # Airport.objects.all().delete()
    # Airline.objects.all().delete()
    
    # ============ 1. AIRLINES (5 records) ============
    airlines = [
        Airline(
            name="Delta Air Lines",
            code="DL",
            country="United States",
            founded_date="1929-03-02",
            website="https://www.delta.com",
            active=True
        ),
        Airline(
            name="United Airlines",
            code="UA",
            country="United States",
            founded_date="1926-04-06",
            website="https://www.united.com",
            active=True
        ),
        Airline(
            name="Emirates",
            code="EK",
            country="United Arab Emirates",
            founded_date="1985-03-25",
            website="https://www.emirates.com",
            active=True
        ),
        Airline(
            name="British Airways",
            code="BA",
            country="United Kingdom",
            founded_date="1974-03-31",
            website="https://www.britishairways.com",
            active=True
        ),
        Airline(
            name="Singapore Airlines",
            code="SQ",
            country="Singapore",
            founded_date="1947-05-01",
            website="https://www.singaporeair.com",
            active=True
        ),
    ]
    
    for airline in airlines:
        airline.save()
        print(f"Created Airline: {airline}")
    
    # ============ 2. AIRPORTS (5 records) ============
    airports = [
        Airport(
            name="John F. Kennedy International Airport",
            code="JFK",
            city="New York",
            country="United States",
            timezone="America/New_York",
            latitude=40.6413,
            longitude=-73.7781
        ),
        Airport(
            name="Los Angeles International Airport",
            code="LAX",
            city="Los Angeles",
            country="United States",
            timezone="America/Los_Angeles",
            latitude=33.9416,
            longitude=-118.4085
        ),
        Airport(
            name="London Heathrow Airport",
            code="LHR",
            city="London",
            country="United Kingdom",
            timezone="Europe/London",
            latitude=51.4700,
            longitude=-0.4543
        ),
        Airport(
            name="Dubai International Airport",
            code="DXB",
            city="Dubai",
            country="United Arab Emirates",
            timezone="Asia/Dubai",
            latitude=25.2532,
            longitude=55.3657
        ),
        Airport(
            name="Singapore Changi Airport",
            code="SIN",
            city="Singapore",
            country="Singapore",
            timezone="Asia/Singapore",
            latitude=1.3644,
            longitude=103.9915
        ),
    ]
    
    for airport in airports:
        airport.save()
        print(f"Created Airport: {airport}")
    
    # ============ 3. AIRCRAFT (5 records) ============
    aircrafts = [
        Aircraft(
            manufacturer="Boeing",
            model="737-800",
            registration_number="N123DL",
            capacity_economy=150,
            capacity_business=20,
            capacity_first=0,
            aircraft_type="NARROW",
            airline=Airline.objects.get(code="DL"),
            manufacture_date="2015-03-15",
            in_service=True
        ),
        Aircraft(
            manufacturer="Airbus",
            model="A380-800",
            registration_number="A6-EDL",
            capacity_economy=400,
            capacity_business=100,
            capacity_first=20,
            aircraft_type="WIDE",
            airline=Airline.objects.get(code="EK"),
            manufacture_date="2018-07-20",
            in_service=True
        ),
        Aircraft(
            manufacturer="Boeing",
            model="787-9 Dreamliner",
            registration_number="N27958",
            capacity_economy=250,
            capacity_business=40,
            capacity_first=10,
            aircraft_type="WIDE",
            airline=Airline.objects.get(code="UA"),
            manufacture_date="2019-01-10",
            in_service=True
        ),
        Aircraft(
            manufacturer="Airbus",
            model="A350-900",
            registration_number="9V-SMF",
            capacity_economy=300,
            capacity_business=50,
            capacity_first=15,
            aircraft_type="WIDE",
            airline=Airline.objects.get(code="SQ"),
            manufacture_date="2020-05-15",
            in_service=True
        ),
        Aircraft(
            manufacturer="Boeing",
            model="777-300ER",
            registration_number="G-STBF",
            capacity_economy=280,
            capacity_business=45,
            capacity_first=12,
            aircraft_type="WIDE",
            airline=Airline.objects.get(code="BA"),
            manufacture_date="2017-11-30",
            in_service=True
        ),
    ]
    
    for aircraft in aircrafts:
        aircraft.save()
        print(f"Created Aircraft: {aircraft}")
    
    # ============ 4. CREW (5 records) ============
    crews = [
        Crew(
            first_name="John",
            last_name="Smith",
            employee_id="PIL001",
            role="PILOT",
            airline=Airline.objects.get(code="DL"),
            date_hired="2010-06-15",
            languages="English, Spanish"
        ),
        Crew(
            first_name="Sarah",
            last_name="Johnson",
            employee_id="PIL002",
            role="CO_PILOT",
            airline=Airline.objects.get(code="EK"),
            date_hired="2015-03-20",
            languages="English, Arabic, French"
        ),
        Crew(
            first_name="Michael",
            last_name="Brown",
            employee_id="FA001",
            role="FLIGHT_ATTENDANT",
            airline=Airline.objects.get(code="UA"),
            date_hired="2018-01-10",
            languages="English, German"
        ),
        Crew(
            first_name="Emily",
            last_name="Davis",
            employee_id="PRS001",
            role="PURSER",
            airline=Airline.objects.get(code="BA"),
            date_hired="2012-11-05",
            languages="English, French, Italian"
        ),
        Crew(
            first_name="David",
            last_name="Wilson",
            employee_id="PIL003",
            role="PILOT",
            airline=Airline.objects.get(code="SQ"),
            date_hired="2014-07-25",
            languages="English, Mandarin"
        ),
    ]
    
    for crew in crews:
        crew.save()
        print(f"Created Crew: {crew}")
    
    # ============ 5. PASSENGERS (5 records) ============
    passengers = [
        Passenger(
            first_name="James",
            last_name="Anderson",
            email="james.anderson@email.com",
            phone="+1-212-555-1234",
            date_of_birth="1985-03-15",
            gender="M",
            passport_number="US123456789",
            passport_country="United States",
            passport_expiry="2030-05-20",
            address="123 Main St, New York, NY 10001"
        ),
        Passenger(
            first_name="Maria",
            last_name="Garcia",
            email="maria.garcia@email.com",
            phone="+1-310-555-5678",
            date_of_birth="1990-07-22",
            gender="F",
            passport_number="US987654321",
            passport_country="United States",
            passport_expiry="2028-11-15",
            address="456 Oak Ave, Los Angeles, CA 90001"
        ),
        Passenger(
            first_name="Ahmed",
            last_name="Khan",
            email="ahmed.khan@email.com",
            phone="+971-50-123-4567",
            date_of_birth="1982-11-05",
            gender="M",
            passport_number="AE12345678",
            passport_country="United Arab Emirates",
            passport_expiry="2029-03-10",
            address="P.O. Box 12345, Dubai, UAE"
        ),
        Passenger(
            first_name="Emma",
            last_name="Thompson",
            email="emma.thompson@email.com",
            phone="+44-20-7946-0123",
            date_of_birth="1995-04-18",
            gender="F",
            passport_number="GB98765432",
            passport_country="United Kingdom",
            passport_expiry="2031-07-25",
            address="221B Baker St, London, UK"
        ),
        Passenger(
            first_name="Wei",
            last_name="Chen",
            email="wei.chen@email.com",
            phone="+65-9123-4567",
            date_of_birth="1988-09-30",
            gender="M",
            passport_number="SG56789123",
            passport_country="Singapore",
            passport_expiry="2027-12-01",
            address="15 Marina Boulevard, Singapore 018906"
        ),
    ]
    
    for passenger in passengers:
        passenger.save()
        print(f"Created Passenger: {passenger}")
    
    # ============ 6. FLIGHTS (5 records) ============
    # Get references
    delta = Airline.objects.get(code="DL")
    united = Airline.objects.get(code="UA")
    emirates = Airline.objects.get(code="EK")
    british = Airline.objects.get(code="BA")
    singapore = Airline.objects.get(code="SQ")
    
    jfk = Airport.objects.get(code="JFK")
    lax = Airport.objects.get(code="LAX")
    lhr = Airport.objects.get(code="LHR")
    dxb = Airport.objects.get(code="DXB")
    sin = Airport.objects.get(code="SIN")
    
    delta_737 = Aircraft.objects.get(registration_number="N123DL")
    emirates_a380 = Aircraft.objects.get(registration_number="A6-EDL")
    united_787 = Aircraft.objects.get(registration_number="N27958")
    singapore_a350 = Aircraft.objects.get(registration_number="9V-SMF")
    british_777 = Aircraft.objects.get(registration_number="G-STBF")
    
    flights = [
        Flight(
            flight_number="DL100",
            airline=delta,
            aircraft=delta_737,
            origin=jfk,
            destination=lax,
            departure_time=datetime.now() + timedelta(days=1, hours=6),
            arrival_time=datetime.now() + timedelta(days=1, hours=9, minutes=30),
            duration_minutes=330,
            status="SCHEDULED",
            base_price=Decimal("299.99")
        ),
        Flight(
            flight_number="EK202",
            airline=emirates,
            aircraft=emirates_a380,
            origin=dxb,
            destination=lhr,
            departure_time=datetime.now() + timedelta(days=2, hours=14),
            arrival_time=datetime.now() + timedelta(days=2, hours=18, minutes=30),
            duration_minutes=450,
            status="SCHEDULED",
            base_price=Decimal("899.99")
        ),
        Flight(
            flight_number="UA888",
            airline=united,
            aircraft=united_787,
            origin=lax,
            destination=sin,
            departure_time=datetime.now() + timedelta(days=3, hours=23),
            arrival_time=datetime.now() + timedelta(days=5, hours=6),
            duration_minutes=1020,
            status="SCHEDULED",
            base_price=Decimal("1299.99")
        ),
        Flight(
            flight_number="BA183",
            airline=british,
            aircraft=british_777,
            origin=lhr,
            destination=jfk,
            departure_time=datetime.now() + timedelta(days=1, hours=10, minutes=30),
            arrival_time=datetime.now() + timedelta(days=1, hours=13, minutes=45),
            duration_minutes=435,
            status="SCHEDULED",
            base_price=Decimal("649.99")
        ),
        Flight(
            flight_number="SQ221",
            airline=singapore,
            aircraft=singapore_a350,
            origin=sin,
            destination=jfk,
            departure_time=datetime.now() + timedelta(days=4, hours=0, minutes=30),
            arrival_time=datetime.now() + timedelta(days=4, hours=7, minutes=45),
            duration_minutes=1095,
            status="SCHEDULED",
            base_price=Decimal("1599.99")
        ),
    ]
    
    for flight in flights:
        flight.save()
        print(f"Created Flight: {flight}")
    
    # ============ 7. BOOKINGS (5 records) ============
    bookings = [
        Booking(
            booking_reference=generate_booking_reference(),
            flight=Flight.objects.get(flight_number="DL100"),
            passenger=Passenger.objects.get(email="james.anderson@email.com"),
            booking_date=datetime.now() - timedelta(days=2),
            status="CONFIRMED",
            total_price=Decimal("299.99"),
            seat_number="12A",
            class_type="ECONOMY",
            special_requests="Vegetarian meal"
        ),
        Booking(
            booking_reference=generate_booking_reference(),
            flight=Flight.objects.get(flight_number="EK202"),
            passenger=Passenger.objects.get(email="maria.garcia@email.com"),
            booking_date=datetime.now() - timedelta(days=1),
            status="CONFIRMED",
            total_price=Decimal("899.99"),
            seat_number="88K",
            class_type="BUSINESS",
            special_requests="Extra legroom"
        ),
        Booking(
            booking_reference=generate_booking_reference(),
            flight=Flight.objects.get(flight_number="UA888"),
            passenger=Passenger.objects.get(email="ahmed.khan@email.com"),
            booking_date=datetime.now() - timedelta(hours=12),
            status="PENDING",
            total_price=Decimal("1299.99"),
            seat_number="45C",
            class_type="ECONOMY",
            special_requests="Window seat preferred"
        ),
        Booking(
            booking_reference=generate_booking_reference(),
            flight=Flight.objects.get(flight_number="BA183"),
            passenger=Passenger.objects.get(email="emma.thompson@email.com"),
            booking_date=datetime.now() - timedelta(days=3),
            status="CONFIRMED",
            total_price=Decimal("649.99"),
            seat_number="2A",
            class_type="FIRST",
            special_requests="Champagne on arrival"
        ),
        Booking(
            booking_reference=generate_booking_reference(),
            flight=Flight.objects.get(flight_number="SQ221"),
            passenger=Passenger.objects.get(email="wei.chen@email.com"),
            booking_date=datetime.now() - timedelta(days=4),
            status="CHECKED_IN",
            total_price=Decimal("1599.99"),
            seat_number="24B",
            class_type="BUSINESS",
            special_requests="Gluten-free meal"
        ),
    ]
    
    for booking in bookings:
        booking.save()
        print(f"Created Booking: {booking}")
    
    # ============ 8. PAYMENTS (5 records) ============
    payments = [
        Payment(
            booking=Booking.objects.get(passenger__email="james.anderson@email.com", flight__flight_number="DL100"),
            amount=Decimal("299.99"),
            payment_method="CARD",
            payment_status="COMPLETED",
            transaction_id="TXN" + ''.join(random.choices(string.digits, k=10)),
            payment_date=datetime.now() - timedelta(days=2, hours=3),
            last_four_digits="1234"
        ),
        Payment(
            booking=Booking.objects.get(passenger__email="maria.garcia@email.com", flight__flight_number="EK202"),
            amount=Decimal("899.99"),
            payment_method="PAYPAL",
            payment_status="COMPLETED",
            transaction_id="TXN" + ''.join(random.choices(string.digits, k=10)),
            payment_date=datetime.now() - timedelta(days=1, hours=5),
            last_four_digits=""
        ),
        Payment(
            booking=Booking.objects.get(passenger__email="emma.thompson@email.com", flight__flight_number="BA183"),
            amount=Decimal("649.99"),
            payment_method="CARD",
            payment_status="COMPLETED",
            transaction_id="TXN" + ''.join(random.choices(string.digits, k=10)),
            payment_date=datetime.now() - timedelta(days=3, hours=2),
            last_four_digits="5678"
        ),
        Payment(
            booking=Booking.objects.get(passenger__email="wei.chen@email.com", flight__flight_number="SQ221"),
            amount=Decimal("1599.99"),
            payment_method="BANK",
            payment_status="COMPLETED",
            transaction_id="TXN" + ''.join(random.choices(string.digits, k=10)),
            payment_date=datetime.now() - timedelta(days=4, hours=6),
            last_four_digits=""
        ),
        # Payment for Ahmed's booking (pending)
        Payment(
            booking=Booking.objects.get(passenger__email="ahmed.khan@email.com", flight__flight_number="UA888"),
            amount=Decimal("1299.99"),
            payment_method="CARD",
            payment_status="PENDING",
            transaction_id="TXN" + ''.join(random.choices(string.digits, k=10)),
            payment_date=datetime.now() - timedelta(hours=11),
            last_four_digits="9012"
        ),
    ]
    
    for payment in payments:
        payment.save()
        print(f"Created Payment: {payment}")
    
    # ============ 9. FLIGHT CREW ASSIGNMENTS (5 records) ============
    flight_crew_assignments = [
        FlightCrew(
            flight=Flight.objects.get(flight_number="DL100"),
            crew_member=Crew.objects.get(employee_id="PIL001"),
            role_on_flight="PILOT"
        ),
        FlightCrew(
            flight=Flight.objects.get(flight_number="EK202"),
            crew_member=Crew.objects.get(employee_id="PIL002"),
            role_on_flight="CO_PILOT"
        ),
        FlightCrew(
            flight=Flight.objects.get(flight_number="UA888"),
            crew_member=Crew.objects.get(employee_id="FA001"),
            role_on_flight="FLIGHT_ATTENDANT"
        ),
        FlightCrew(
            flight=Flight.objects.get(flight_number="BA183"),
            crew_member=Crew.objects.get(employee_id="PRS001"),
            role_on_flight="PURSER"
        ),
        FlightCrew(
            flight=Flight.objects.get(flight_number="SQ221"),
            crew_member=Crew.objects.get(employee_id="PIL003"),
            role_on_flight="PILOT"
        ),
    ]
    
    for assignment in flight_crew_assignments:
        assignment.save()
        print(f"Created Flight Crew Assignment: {assignment}")
    
    print("\n" + "="*50)
    print("Data population completed successfully!")
    print("="*50)
    print("\nSummary:")
    print(f"✓ {Airline.objects.count()} Airlines")
    print(f"✓ {Airport.objects.count()} Airports")
    print(f"✓ {Aircraft.objects.count()} Aircraft")
    print(f"✓ {Crew.objects.count()} Crew Members")
    print(f"✓ {Passenger.objects.count()} Passengers")
    print(f"✓ {Flight.objects.count()} Flights")
    print(f"✓ {Booking.objects.count()} Bookings")
    print(f"✓ {Payment.objects.count()} Payments")
    print(f"✓ {FlightCrew.objects.count()} Flight Crew Assignments")

if __name__ == "__main__":
    load_data()
```

## Run the Script

```bash
# Make sure you're in the project root directory
python load_data.py
```

## After Loading Data

1. **Access Admin**: http://127.0.0.1:8000/admin/
2. **Login** with superuser credentials
3. **Explore** each model to see data:
   - Airlines: See all 5 airlines
   - Flights: View schedules
   - Bookings: Manage reservations
   - Payments: Track transactions
