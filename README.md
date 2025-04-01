# Complete Guide to Creating the [tourgrid_copy] Database

This guide provides step-by-step instructions for creating the tourgrid_copy database, including tables, data, views, triggers, stored procedures, and complex queries.



by Usham Roy

## Step 1: Create the Database

```sql
-- Create the database
CREATE DATABASE IF NOT EXISTS tourgrid_copy;

-- Select the database
USE tourgrid_copy;

```

## Step 2: Create Tables

```sql
-- Create customers table
CREATE TABLE customers (
  customer_id int(11) NOT NULL AUTO_INCREMENT,
  first_name varchar(50) NOT NULL,
  last_name varchar(50) NOT NULL,
  email varchar(100) NOT NULL,
  phone varchar(20) DEFAULT NULL,
  address text DEFAULT NULL,
  date_of_birth date DEFAULT NULL,
  nationality varchar(50) DEFAULT NULL,
  passport_number varchar(20) DEFAULT NULL,
  emergency_contact_name varchar(100) DEFAULT NULL,
  emergency_contact_phone varchar(20) DEFAULT NULL,
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  updated_at timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (customer_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create package table
CREATE TABLE package (
  id int(11) NOT NULL AUTO_INCREMENT,
  package_id varchar(10) NOT NULL,
  location varchar(255) NOT NULL,
  category varchar(50) NOT NULL,
  details text NOT NULL,
  LANGUAGE varchar(50) NOT NULL,
  description text NOT NULL,
  travel_mode varchar(50) NOT NULL,
  journey_time varchar(100) NOT NULL,
  days_nights varchar(50) NOT NULL,
  tour_description text NOT NULL,
  location_map varchar(255) DEFAULT NULL,
  pictures longtext DEFAULT NULL,
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  updated_at timestamp NOT NULL DEFAULT pbcurrent_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (id),
  UNIQUE KEY package_id (package_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create hotels table
CREATE TABLE hotels (
  id int(11) NOT NULL AUTO_INCREMENT,
  hotel_id varchar(10) NOT NULL,
  hotel_name varchar(255) NOT NULL,
  location varchar(255) NOT NULL,
  address text NOT NULL,
  category varchar(50) NOT NULL,
  description text NOT NULL,
  amenities text DEFAULT NULL,
  check_in_time time NOT NULL,
  check_out_time time NOT NULL,
  contact_number varchar(20) NOT NULL,
  email varchar(255) NOT NULL,
  website varchar(255) DEFAULT NULL,
  pictures longtext DEFAULT NULL,
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  updated_at timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (id),
  UNIQUE KEY hotel_id (hotel_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create tour_schedules table
CREATE TABLE tour_schedules (
  id int(11) NOT NULL AUTO_INCREMENT,
  schedule_id varchar(20) NOT NULL,
  package_id varchar(50) NOT NULL,
  start_date date NOT NULL,
  start_time time NOT NULL,
  end_date date NOT NULL,
  end_time time NOT NULL,
  capacity int(11) NOT NULL,
  starting_location varchar(255) NOT NULL,
  ending_location varchar(255) NOT NULL,
  max_travelers int(11) NOT NULL,
  price_per_person decimal(10,2) NOT NULL,
  discount decimal(5,2) DEFAULT 0.00,
  hotel_assignments longtext DEFAULT NULL,
  status enum('active','pending','completed','cancelled') NOT NULL,
  notes text DEFAULT NULL,
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  updated_at timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (id),
  UNIQUE KEY schedule_id (schedule_id),
  KEY package_id (package_id),
  CONSTRAINT tour_schedules_ibfk_1 FOREIGN KEY (package_id) REFERENCES package (package_id) ON DELETE CASCADE ON UPDATE CASCADE
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create bookings table
CREATE TABLE bookings (
  booking_id varchar(20) NOT NULL,
  customer_id int(11) NOT NULL,
  schedule_id varchar(20) NOT NULL,
  booking_date date NOT NULL,
  number_of_travelers int(11) NOT NULL,
  total_price decimal(10,2) NOT NULL,
  status enum('pending','confirmed','cancelled','completed') DEFAULT 'pending',
  payment_status enum('unpaid','partial','paid') DEFAULT 'unpaid',
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (booking_id),
  KEY customer_id (customer_id),
  KEY schedule_id (schedule_id),
  CONSTRAINT bookings_ibfk_1 FOREIGN KEY (customer_id) REFERENCES customers (customer_id) ON DELETE RESTRICT ON UPDATE RESTRICT,
  CONSTRAINT bookings_ibfk_2 FOREIGN KEY (schedule_id) REFERENCES tour_schedules (schedule_id) ON DELETE RESTRICT ON UPDATE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create payments table
CREATE TABLE payments (
  payment_id int(11) NOT NULL AUTO_INCREMENT,
  booking_id varchar(20) NOT NULL,
  amount decimal(10,2) NOT NULL,
  payment_date date NOT NULL,
  payment_method enum('Credit Card','Debit Card','PayPal','Bank Transfer','Cash') NOT NULL,
  status enum('Pending','Completed','Failed','Refunded') NOT NULL,
  transaction_id varchar(100) DEFAULT NULL,
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  updated_at timestamp NOT NULL DEFAULT current_timestamp() ON UPDATE current_timestamp(),
  PRIMARY KEY (payment_id),
  KEY booking_id (booking_id),
  CONSTRAINT payments_ibfk_1 FOREIGN KEY (booking_id) REFERENCES bookings (booking_id) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create feedback table
CREATE TABLE feedback (
  feedback_id int(11) NOT NULL AUTO_INCREMENT,
  booking_id varchar(20) NOT NULL,
  rating int(11) NOT NULL,
  comment text DEFAULT NULL,
  created_at timestamp NOT NULL DEFAULT current_timestamp(),
  PRIMARY KEY (feedback_id),
  KEY booking_id (booking_id),
  CONSTRAINT feedback_ibfk_1 FOREIGN KEY (booking_id) REFERENCES bookings (booking_id) ON DELETE CASCADE ON UPDATE RESTRICT
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create contact_details table
CREATE TABLE contact_details (
  serial int(11) NOT NULL AUTO_INCREMENT,
  name varchar(50) NOT NULL,
  mail varchar(100) NOT NULL,
  phone varchar(15) NOT NULL,
  time varchar(30) NOT NULL,
  purpose varchar(200) NOT NULL,
  PRIMARY KEY (serial)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4;

-- Create system_log table for stored procedures
CREATE TABLE system_log (
  log_id INT AUTO_INCREMENT PRIMARY KEY,
  action VARCHAR(50) NOT NULL,
  description TEXT,
  created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

-- Create customer_audit table for triggers
CREATE TABLE customer_audit (
  audit_id INT AUTO_INCREMENT PRIMARY KEY,
  customer_id INT NOT NULL,
  action_type VARCHAR(10) NOT NULL,
  changed_fields TEXT,
  changed_by VARCHAR(50),
  change_timestamp TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);

```

## Step 3: Alter Tables as Needed

```sql
-- Alter payments table to add UPI as a payment method
ALTER TABLE payments 
MODIFY COLUMN payment_method ENUM('Credit Card', 'Debit Card', 'PayPal', 'Bank Transfer', 'Cash', 'UPI') NOT NULL;
```

## Step 4: Insert Sample Data

```sql
-- Insert dummy customers with Indian names
INSERT INTO customers (first_name, last_name, email, phone, address, date_of_birth, nationality, passport_number, emergency_contact_name, emergency_contact_phone) VALUES
('Rajesh', 'Sharma', 'rajesh.sharma@example.com', '+91-9876543210', '42 Anna Salai, Chennai, Tamil Nadu 600002', '1985-03-15', 'Indian', 'IN123456789', 'Priya Sharma', '+91-9876543211'),
('Priya', 'Patel', 'priya.patel@example.com', '+91-9876543212', '15 T Nagar, Chennai, Tamil Nadu 600017', '1990-07-22', 'Indian', 'IN234567890', 'Vikram Patel', '+91-9876543213'),
('Vikram', 'Singh', 'vikram.singh@example.com', '+91-9876543214', '78 Besant Nagar, Chennai, Tamil Nadu 600090', '1982-11-30', 'Indian', 'IN345678901', 'Anita Singh', '+91-9876543215'),
('Anita', 'Gupta', 'anita.gupta@example.com', '+91-9876543216', '23 Adyar, Chennai, Tamil Nadu 600020', '1988-05-12', 'Indian', 'IN456789012', 'Rahul Gupta', '+91-9876543217'),
('Rahul', 'Verma', 'rahul.verma@example.com', '+91-9876543218', '56 Velachery, Chennai, Tamil Nadu 600042', '1975-09-03', 'Indian', 'IN567890123', 'Meera Verma', '+91-9876543219'),
('Meera', 'Reddy', 'meera.reddy@example.com', '+91-9876543220', '89 Mylapore, Chennai, Tamil Nadu 600004', '1992-01-25', 'Indian', 'IN678901234', 'Suresh Reddy', '+91-9876543221'),
('Suresh', 'Kumar', 'suresh.kumar@example.com', '+91-9876543222', '34 Nungambakkam, Chennai, Tamil Nadu 600034', '1980-06-18', 'Indian', 'IN789012345', 'Lakshmi Kumar', '+91-9876543223'),
('Lakshmi', 'Iyer', 'lakshmi.iyer@example.com', '+91-9876543224', '67 Egmore, Chennai, Tamil Nadu 600008', '1995-12-07', 'Indian', 'IN890123456', 'Karthik Iyer', '+91-9876543225'),
('Karthik', 'Nair', 'karthik.nair@example.com', '+91-9876543226', '12 Porur, Chennai, Tamil Nadu 600116', '1978-04-29', 'Indian', 'IN901234567', 'Divya Nair', '+91-9876543227'),
('Divya', 'Menon', 'divya.menon@example.com', '+91-9876543228', '45 Tambaram, Chennai, Tamil Nadu 600045', '1993-08-14', 'Indian', 'IN012345678', 'Arun Menon', '+91-9876543229');

-- Insert dummy packages with Indian locations
INSERT INTO package (package_id, location, category, details, LANGUAGE, description, travel_mode, journey_time, days_nights, tour_description, location_map, pictures) VALUES
('PKG001', 'Chennai, Tamil Nadu', 'City Tour', 'Explore the cultural capital of South India', 'English, Tamil', 'Experience the rich heritage and modern attractions of Chennai', 'Flight + Bus', 'Local transportation', '3 days, 2 nights', 'Day 1: Marina Beach, Fort St. George, and San Thome Basilica\nDay 2: Kapaleeshwarar Temple, Government Museum, and shopping at T Nagar\nDay 3: Mahabalipuram day trip', 'chennai_map.jpg', 'chennai1.jpg,chennai2.jpg,chennai3.jpg'),
('PKG002', 'Pondicherry', 'Beach & Heritage', 'Discover the French colonial charm of Pondicherry', 'English, Tamil, French', 'Experience the unique blend of Indian and French culture in this coastal town', 'Bus from Chennai', '3 hours journey', '2 days, 1 night', 'Day 1: French Quarter, Promenade Beach, and Aurobindo Ashram\nDay 2: Auroville, Paradise Beach, and return to Chennai', 'pondicherry_map.jpg', 'pondicherry1.jpg,pondicherry2.jpg'),
('PKG003', 'Ooty, Tamil Nadu', 'Hill Station', 'Relax in the Queen of Hill Stations', 'English, Tamil', 'Enjoy the cool climate and scenic beauty of the Nilgiri Hills', 'Train + Bus', '8 hours journey', '4 days, 3 nights', 'Day 1: Arrival and Ooty Lake\nDay 2: Botanical Gardens and Doddabetta Peak\nDay 3: Coonoor and Nilgiri Mountain Railway\nDay 4: Tea Plantations and return to Chennai', 'ooty_map.jpg', 'ooty1.jpg,ooty2.jpg,ooty3.jpg'),
('PKG004', 'Madurai, Tamil Nadu', 'Temple Tour', 'Explore the ancient temple city of Tamil Nadu', 'English, Tamil', 'Journey through time as you visit the magnificent temples of Madurai', 'Train from Chennai', '7 hours journey', '3 days, 2 nights', 'Day 1: Arrival and Meenakshi Amman Temple\nDay 2: Thirumalai Nayakkar Palace and Gandhi Museum\nDay 3: Alagar Koil and return to Chennai', 'madurai_map.jpg', 'madurai1.jpg,madurai2.jpg'),
('PKG005', 'Kerala Backwaters', 'Nature & Relaxation', 'Experience the serene backwaters of Kerala', 'English, Malayalam', 'Discover the natural beauty of Kerala with houseboat stays and ayurvedic treatments', 'Flight to Kochi + Bus', '1.5 hours flight, 1 hour bus', '5 days, 4 nights', 'Day 1: Arrival in Kochi and Fort Kochi tour\nDay 2: Transfer to Alleppey\nDay 3: Houseboat cruise through backwaters\nDay 4: Kumarakom Bird Sanctuary\nDay 5: Return to Chennai', 'kerala_map.jpg', 'kerala1.jpg,kerala2.jpg,kerala3.jpg');

-- Insert dummy hotels
INSERT INTO hotels (hotel_id, hotel_name, location, address, category, description, amenities, check_in_time, check_out_time, contact_number, email, website, pictures) VALUES
('HTL001', 'Taj Coromandel', 'Chennai, Tamil Nadu', '37 Mahatma Gandhi Road, Nungambakkam, Chennai 600034', 'Luxury', '5-star luxury hotel in the heart of Chennai', 'Free WiFi, Spa, Pool, Restaurant, Bar, Gym, Room Service', '14:00:00', '12:00:00', '+91-44-6600-2827', 'info@tajcoromandel.com', 'www.tajhotels.com', 'taj_chennai1.jpg,taj_chennai2.jpg'),
('HTL002', 'Le Pondy', 'Pondicherry', 'No. 3, Lake View Road, Pondicherry 605001', 'Resort', 'Beachfront resort with French colonial architecture', 'Free WiFi, Spa, Pool, Restaurant, Bar, Beach Access', '15:00:00', '11:00:00', '+91-413-2205-050', 'info@lepondy.com', 'www.lepondy.com', 'lepondy1.jpg,lepondy2.jpg'),
('HTL003', 'Taj Savoy', 'Ooty, Tamil Nadu', 'Sylks Road, Ooty 643001', 'Heritage', 'Colonial-era heritage hotel with beautiful gardens', 'Free WiFi, Restaurant, Bar, Garden, Room Service', '14:00:00', '12:00:00', '+91-423-2444-142', 'info@tajsavoy.com', 'www.tajhotels.com', 'taj_ooty1.jpg,taj_ooty2.jpg'),
('HTL004', 'Heritage Madurai', 'Madurai, Tamil Nadu', '11 Melakkal Main Road, Madurai 625010', 'Heritage', 'Luxury heritage hotel with traditional Tamil architecture', 'Free WiFi, Pool, Restaurant, Bar, Spa', '13:00:00', '11:00:00', '+91-452-2385-455', 'info@heritagemadurai.com', 'www.heritagemadurai.com', 'heritage_madurai1.jpg,heritage_madurai2.jpg'),
('HTL005', 'Kumarakom Lake Resort', 'Kumarakom, Kerala', 'Kumarakom North, Kottayam 686563', 'Luxury', 'Luxury resort on the banks of Vembanad Lake', 'Free WiFi, Spa, Pool, Restaurant, Bar, Ayurvedic Center', '14:00:00', '12:00:00', '+91-481-2524-900', 'info@kumarakomlakeresort.com', 'www.kumarakomlakeresort.com', 'kumarakom1.jpg,kumarakom2.jpg');

-- Insert dummy tour schedules
INSERT INTO tour_schedules (schedule_id, package_id, start_date, start_time, end_date, end_time, capacity, starting_location, ending_location, max_travelers, price_per_person, discount, hotel_assignments, status, notes) VALUES
('SCH001', 'PKG001', '2025-06-15', '09:00:00', '2025-06-17', '17:00:00', 20, 'Chennai International Airport', 'Chennai International Airport', 20, 15000.00, 0.00, 'HTL001', 'active', 'Summer tour of Chennai'),
('SCH002', 'PKG001', '2025-07-10', '09:00:00', '2025-07-12', '17:00:00', 20, 'Chennai International Airport', 'Chennai International Airport', 20, 16500.00, 5.00, 'HTL001', 'active', 'Monsoon special Chennai tour'),
('SCH003', 'PKG002', '2025-05-20', '10:00:00', '2025-05-21', '18:00:00', 15, 'Chennai Central Bus Station', 'Chennai Central Bus Station', 15, 8000.00, 0.00, 'HTL002', 'active', 'Weekend getaway to Pondicherry'),
('SCH004', 'PKG003', '2025-06-05', '08:00:00', '2025-06-08', '19:00:00', 25, 'Chennai Central Railway Station', 'Chennai Central Railway Station', 25, 20000.00, 10.00, 'HTL003', 'active', 'Summer escape to Ooty'),
('SCH005', 'PKG004', '2025-09-15', '07:00:00', '2025-09-17', '20:00:00', 18, 'Chennai Central Railway Station', 'Chennai Central Railway Station', 18, 12000.00, 0.00, 'HTL004', 'pending', 'Temple tour of Madurai'),
('SCH006', 'PKG005', '2025-08-01', '09:00:00', '2025-08-05', '17:00:00', 22, 'Chennai International Airport', 'Chennai International Airport', 22, 35000.00, 0.00, 'HTL005', 'active', 'Kerala backwaters experience'),
('SCH007', 'PKG001', '2025-04-10', '09:00:00', '2025-04-12', '17:00:00', 20, 'Chennai International Airport', 'Chennai International Airport', 20, 14000.00, 0.00, 'HTL001', 'completed', 'Spring tour of Chennai'),
('SCH008', 'PKG002', '2025-03-15', '10:00:00', '2025-03-16', '18:00:00', 15, 'Chennai Central Bus Station', 'Chennai Central Bus Station', 15, 7500.00, 5.00, 'HTL002', 'completed', 'Pondicherry weekend retreat');

-- Insert dummy bookings
INSERT INTO bookings (booking_id, customer_id, schedule_id, booking_date, number_of_travelers, total_price, status, payment_status, created_at) VALUES
('BK001', 1, 'SCH001', '2025-03-10', 2, 30000.00, 'confirmed', 'paid', '2025-03-10 14:30:00'),
('BK002', 2, 'SCH001', '2025-03-15', 1, 15000.00, 'confirmed', 'paid', '2025-03-15 10:45:00'),
('BK003', 3, 'SCH002', '2025-04-05', 3, 47025.00, 'confirmed', 'partial', '2025-04-05 16:20:00'),
('BK004', 4, 'SCH003', '2025-03-20', 2, 16000.00, 'confirmed', 'paid', '2025-03-20 09:15:00'),
('BK005', 5, 'SCH004', '2025-04-10', 4, 72000.00, 'confirmed', 'partial', '2025-04-10 11:30:00'),
('BK006', 6, 'SCH005', '2025-05-01', 2, 24000.00, 'pending', 'unpaid', '2025-05-01 13:45:00'),
('BK007', 7, 'SCH006', '2025-04-15', 3, 105000.00, 'confirmed', 'paid', '2025-04-15 15:10:00'),
('BK008', 8, 'SCH007', '2025-01-20', 2, 28000.00, 'completed', 'paid', '2025-01-20 10:00:00'),
('BK009', 9, 'SCH008', '2025-01-15', 1, 7125.00, 'completed', 'paid', '2025-01-15 14:25:00'),
('BK010', 10, 'SCH001', '2025-03-25', 2, 30000.00, 'confirmed', 'partial', '2025-03-25 16:50:00');

-- Insert dummy payments
INSERT INTO payments (booking_id, amount, payment_date, payment_method, status, transaction_id, created_at) VALUES
('BK001', 30000.00, '2025-03-10', 'Credit Card', 'Completed', 'TRX001', '2025-03-10 14:35:00'),
('BK002', 15000.00, '2025-03-15', 'UPI', 'Completed', 'TRX002', '2025-03-15 10:50:00'),
('BK003', 25000.00, '2025-04-05', 'Credit Card', 'Completed', 'TRX003', '2025-04-05 16:25:00'),
('BK004', 16000.00, '2025-03-20', 'Bank Transfer', 'Completed', 'TRX004', '2025-03-20 09:20:00'),
('BK005', 40000.00, '2025-04-10', 'Credit Card', 'Completed', 'TRX005', '2025-04-10 11:35:00'),
('BK007', 105000.00, '2025-04-15', 'Credit Card', 'Completed', 'TRX007', '2025-04-15 15:15:00'),
('BK008', 28000.00, '2025-01-20', 'Debit Card', 'Completed', 'TRX008', '2025-01-20 10:05:00'),
('BK009', 7125.00, '2025-01-15', 'UPI', 'Completed', 'TRX009', '2025-01-15 14:30:00'),
('BK010', 15000.00, '2025-03-25', 'Credit Card', 'Completed', 'TRX010', '2025-03-25 16:55:00');

-- Insert dummy feedback
INSERT INTO feedback (booking_id, rating, comment, created_at) VALUES
('BK008', 5, 'Amazing tour of Chennai! Our guide was knowledgeable and friendly. The Taj Coromandel was perfect.', '2025-04-15 09:30:00'),
('BK009', 4, 'Great weekend in Pondicherry. The French Quarter was beautiful. Would have liked more free time at the beach.', '2025-03-25 14:45:00');

-- Insert dummy contact details
INSERT INTO contact_details (name, mail, phone, time, purpose) VALUES
('Aravind Kumar', 'aravind.k@example.com', '+91-9876543230', 'Morning', 'Inquiry about Chennai city tour'),
('Shalini Rajan', 'shalini.r@example.com', '+91-9876543231', 'Afternoon', 'Question about hotel accommodations in Ooty'),
('Venkat Krishnan', 'venkat.k@example.com', '+91-9876543232', 'Evening', 'Request for group booking information for Kerala package'),
('Deepa Sundaram', 'deepa.s@example.com', '+91-9876543233', 'Morning', 'Feedback on website'),
('Mohan Rao', 'mohan.r@example.com', '+91-9876543234', 'Afternoon', 'Job application inquiry for tour guide position');

```

## Step 5: Create Complex Queries

### 1. Find all bookings with customer details and tour information

```sql
SELECT 
    b.booking_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.email,
    c.phone,
    ts.schedule_id,
    p.package_id,
    p.location,
    p.category,
    ts.start_date,
    ts.end_date,
    b.number_of_travelers,
    b.total_price,
    b.status,
    b.payment_status
FROM 
    bookings b
JOIN 
    customers c ON b.customer_id = c.customer_id
JOIN 
    tour_schedules ts ON b.schedule_id = ts.schedule_id
JOIN 
    package p ON ts.package_id = p.package_id
WHERE 
    b.status = 'confirmed'
    AND ts.start_date BETWEEN CURDATE() AND DATE_ADD(CURDATE(), INTERVAL 30 DAY)
ORDER BY 
    ts.start_date;

```

### 2. Calculate revenue by package category and month

```sql
SELECT 
    p.category,
    YEAR(b.booking_date) AS booking_year,
    MONTH(b.booking_date) AS booking_month,
    COUNT(b.booking_id) AS total_bookings,
    SUM(b.number_of_travelers) AS total_travelers,
    SUM(b.total_price) AS total_revenue
FROM 
    bookings b
JOIN 
    tour_schedules ts ON b.schedule_id = ts.schedule_id
JOIN 
    package p ON ts.package_id = p.package_id
WHERE 
    b.status IN ('confirmed', 'completed')
GROUP BY 
    p.category, 
    YEAR(b.booking_date), 
    MONTH(b.booking_date)
ORDER BY 
    booking_year DESC, 
    booking_month DESC, 
    total_revenue DESC;

```

### 3. Find customers who have booked multiple tours

```sql
SELECT 
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.email,
    COUNT(b.booking_id) AS total_bookings,
    SUM(b.total_price) AS total_spent,
    MIN(b.booking_date) AS first_booking,
    MAX(b.booking_date) AS last_booking
FROM 
    customers c
JOIN 
    bookings b ON c.customer_id = b.customer_id
GROUP BY 
    c.customer_id, customer_name, c.email
HAVING 
    COUNT(b.booking_id) > 1
ORDER BY 
    total_bookings DESC, 
    total_spent DESC;

```

### 4. Find Available Tours for a Specific Date Range with Discount Information

```sql

SELECT 
    ts.schedule_id,
    p.location,
    p.category,
    p.days_nights,
    ts.start_date,
    ts.end_date,
    ts.price_per_person,
    ts.discount,
    (ts.price_per_person - (ts.price_per_person * ts.discount / 100)) AS discounted_price,
    (ts.capacity - COALESCE(
        (SELECT SUM(b.number_of_travelers) 
         FROM bookings b 
         WHERE b.schedule_id = ts.schedule_id AND b.status IN ('confirmed', 'pending')), 
        0)) AS available_spots
FROM 
    tour_schedules ts
JOIN 
    package p ON ts.package_id = p.package_id
WHERE 
    ts.status = 'active'
    AND ts.start_date BETWEEN '2025-05-01' AND '2025-06-30'
    AND (ts.capacity - COALESCE(
        (SELECT SUM(b.number_of_travelers) 
         FROM bookings b 
         WHERE b.schedule_id = ts.schedule_id AND b.status IN ('confirmed', 'pending')), 
        0)) > 0
ORDER BY 
    ts.start_date, p.location;

```

### 5. Calculate Tour Popularity and Rating

```sql
SELECT 
    p.package_id,
    p.location,
    p.category,
    COUNT(DISTINCT b.booking_id) AS total_bookings,
    SUM(b.number_of_travelers) AS total_travelers,
    ROUND(AVG(COALESCE(f.rating, 0)), 1) AS average_rating,
    COUNT(f.feedback_id) AS number_of_reviews
FROM 
    package p
LEFT JOIN 
    tour_schedules ts ON p.package_id = ts.package_id
LEFT JOIN 
    bookings b ON ts.schedule_id = b.schedule_id AND b.status IN ('confirmed', 'completed')
LEFT JOIN 
    feedback f ON b.booking_id = f.booking_id
GROUP BY 
    p.package_id, p.location, p.category
ORDER BY 
    total_bookings DESC, average_rating DESC;

```

## Step 6: Create Views

```sql

-- Create active_tours view
CREATE VIEW active_tours AS
SELECT 
    ts.schedule_id,
    p.package_id,
    p.location,
    p.category,
    p.days_nights,
    ts.start_date,
    ts.end_date,
    ts.capacity,
    ts.max_travelers,
    ts.price_per_person,
    ts.discount,
    (ts.capacity - COALESCE(
        (SELECT SUM(b.number_of_travelers) 
         FROM bookings b 
         WHERE b.schedule_id = ts.schedule_id AND b.status IN ('confirmed', 'pending')), 
        0)) AS available_spots
FROM 
    tour_schedules ts
JOIN 
    package p ON ts.package_id = p.package_id
WHERE 
    ts.status = 'active'
    AND ts.start_date >= CURDATE();

-- Create customer_booking_history view
CREATE VIEW customer_booking_history AS
SELECT 
    c.customer_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    c.email,
    c.phone,
    b.booking_id,
    b.booking_date,
    p.location,
    p.category,
    ts.start_date,
    ts.end_date,
    b.number_of_travelers,
    b.total_price,
    b.status,
    b.payment_status,
    COALESCE(f.rating, 0) AS rating,
    f.comment
FROM 
    customers c
LEFT JOIN 
    bookings b ON c.customer_id = b.customer_id
LEFT JOIN 
    tour_schedules ts ON b.schedule_id = ts.schedule_id
LEFT JOIN 
    package p ON ts.package_id = p.package_id
LEFT JOIN 
    feedback f ON b.booking_id = f.booking_id;

-- Create payment_summary view
CREATE VIEW payment_summary AS
SELECT 
    b.booking_id,
    CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
    p.location AS tour_location,
    ts.start_date,
    b.total_price AS booking_total,
    COALESCE(SUM(py.amount), 0) AS amount_paid,
    (b.total_price - COALESCE(SUM(py.amount), 0)) AS balance_due,
    b.payment_status
FROM 
    bookings b
JOIN 
    customers c ON b.customer_id = c.customer_id
JOIN 
    tour_schedules ts ON b.schedule_id = ts.schedule_id
JOIN 
    package p ON ts.package_id = p.package_id
LEFT JOIN 
    payments py ON b.booking_id = py.booking_id AND py.status = 'Completed'
GROUP BY 
    b.booking_id, customer_name, tour_location, ts.start_date, booking_total, b.payment_status;

-- Create popular_destinations view
CREATE VIEW popular_destinations AS
SELECT 
    p.location,
    COUNT(b.booking_id) AS total_bookings,
    SUM(b.number_of_travelers) AS total_travelers,
    ROUND(AVG(COALESCE(f.rating, 0)), 1) AS average_rating
FROM 
    package p
LEFT JOIN 
    tour_schedules ts ON p.package_id = ts.package_id
LEFT JOIN 
    bookings b ON ts.schedule_id = b.schedule_id
LEFT JOIN 
    feedback f ON b.booking_id = f.booking_id
GROUP BY 
    p.location
ORDER BY 
    total_bookings DESC, average_rating DESC;

```

## Step 7: Create Triggers

```sql
-- Change delimiter for triggers
DELIMITER //

-- Create update_booking_payment_status trigger
CREATE TRIGGER update_booking_payment_status
AFTER INSERT ON payments
FOR EACH ROW
BEGIN
    DECLARE total_amount DECIMAL(10,2);
    DECLARE paid_amount DECIMAL(10,2);
    
    -- Get the total booking amount
    SELECT total_price INTO total_amount 
    FROM bookings 
    WHERE booking_id = NEW.booking_id;
    
    -- Calculate total paid amount
    SELECT COALESCE(SUM(amount), 0) INTO paid_amount 
    FROM payments 
    WHERE booking_id = NEW.booking_id 
    AND status = 'Completed';
    
    -- Update booking payment status
    IF paid_amount >= total_amount THEN
        UPDATE bookings SET payment_status = 'paid' WHERE booking_id = NEW.booking_id;
    ELSEIF paid_amount > 0 THEN
        UPDATE bookings SET payment_status = 'partial' WHERE booking_id = NEW.booking_id;
    ELSE
        UPDATE bookings SET payment_status = 'unpaid' WHERE booking_id = NEW.booking_id;
    END IF;
END //

-- Create check_tour_capacity trigger
CREATE TRIGGER check_tour_capacity
BEFORE INSERT ON bookings
FOR EACH ROW
BEGIN
    DECLARE available_capacity INT;
    DECLARE current_bookings INT;
    
    -- Get the maximum capacity for the tour
    SELECT capacity INTO available_capacity
    FROM tour_schedules
    WHERE schedule_id = NEW.schedule_id;
    
    -- Get current number of travelers booked
    SELECT COALESCE(SUM(number_of_travelers), 0) INTO current_bookings
    FROM bookings
    WHERE schedule_id = NEW.schedule_id
    AND status IN ('confirmed', 'pending');
    
    -- Check if adding new booking exceeds capacity
    IF (current_bookings + NEW.number_of_travelers) > available_capacity THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Booking exceeds tour capacity';
    END IF;
END //

-- Create update_tour_status trigger
CREATE TRIGGER update_tour_status
BEFORE UPDATE ON tour_schedules
FOR EACH ROW
BEGIN
    -- Automatically mark tours as completed after end date
    IF NEW.end_date < CURDATE() AND NEW.status = 'active' THEN
        SET NEW.status = 'completed';
    END IF;
    
    -- Don't allow changing status of completed tours back to active
    IF OLD.status = 'completed' AND NEW.status = 'active' THEN
        SIGNAL SQLSTATE '45000'
        SET MESSAGE_TEXT = 'Cannot change completed tour back to active';
    END IF;
END //

-- Create customer_after_update trigger
CREATE TRIGGER customer_after_update
AFTER UPDATE ON customers
FOR EACH ROW
BEGIN
    DECLARE changes TEXT DEFAULT '';
    
    IF OLD.first_name != NEW.first_name THEN
        SET changes = CONCAT(changes, 'first_name: ', OLD.first_name, ' -> ', NEW.first_name, '; ');
    END IF;
    
    IF OLD.last_name != NEW.last_name THEN
        SET changes = CONCAT(changes, 'last_name: ', OLD.last_name, ' -> ', NEW.last_name, '; ');
    END IF;
    
    IF OLD.email != NEW.email THEN
        SET changes = CONCAT(changes, 'email: ', OLD.email, ' -> ', NEW.email, '; ');
    END IF;
    
    IF OLD.phone != NEW.phone THEN
        SET changes = CONCAT(changes, 'phone: ', OLD.phone, ' -> ', NEW.phone, '; ');
    END IF;
    
    IF OLD.address != NEW.address THEN
        SET changes = CONCAT(changes, 'address changed; ');
    END IF;
    
    IF changes != '' THEN
        INSERT INTO customer_audit (customer_id, action_type, changed_fields, changed_by)
        VALUES (NEW.customer_id, 'UPDATE', changes, CURRENT_USER());
    END IF;
END //

-- Reset delimiter
DELIMITER ;

```


## Step 8: Create Stored Procedures with Cursors

```sql

-- Change delimiter for stored procedures
DELIMITER //

-- Create process_pending_bookings procedure
CREATE PROCEDURE process_pending_bookings()
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE b_id VARCHAR(20);
    DECLARE c_email VARCHAR(100);
    DECLARE c_name VARCHAR(100);
    DECLARE tour_name VARCHAR(255);
    DECLARE tour_date DATE;
    
    -- Cursor for pending bookings
    DECLARE pending_bookings CURSOR FOR
        SELECT 
            b.booking_id, 
            c.email, 
            CONCAT(c.first_name, ' ', c.last_name) AS customer_name,
            p.location,
            ts.start_date
        FROM 
            bookings b
        JOIN 
            customers c ON b.customer_id = c.customer_id
        JOIN 
            tour_schedules ts ON b.schedule_id = ts.schedule_id
        JOIN 
            package p ON ts.package_id = p.package_id
        WHERE 
            b.status = 'pending'
            AND b.payment_status IN ('partial', 'paid');
            
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    
    -- Open cursor
    OPEN pending_bookings;
    
    -- Start processing
    read_loop: LOOP
        FETCH pending_bookings INTO b_id, c_email, c_name, tour_name, tour_date;
        
        IF done THEN
            LEAVE read_loop;
        END IF;
        
        -- Update booking status to confirmed
        UPDATE bookings SET status = 'confirmed' WHERE booking_id = b_id;
        
        -- Here you would typically call a procedure to send email confirmation
        -- For demonstration, we'll just log the action
        INSERT INTO system_log (action, description) 
        VALUES (
            'BOOKING_CONFIRMED', 
            CONCAT('Booking ', b_id, ' confirmed for ', c_name, ' (', c_email, ') for ', 
                   tour_name, ' starting on ', tour_date)
        );
        
    END LOOP;
    
    CLOSE pending_bookings;
    
    -- Return summary
    SELECT 'Pending bookings processed successfully' AS result;
END //

-- Create generate_monthly_revenue_report procedure
CREATE PROCEDURE generate_monthly_revenue_report(IN report_year INT, IN report_month INT)
BEGIN
    DECLARE done INT DEFAULT FALSE;
    DECLARE tour_location VARCHAR(255);
    DECLARE tour_category VARCHAR(50);
    DECLARE booking_count INT;
    DECLARE total_revenue DECIMAL(12,2);
    DECLARE total_travelers INT;
    
    -- Create a temporary table to store the report
    CREATE TEMPORARY TABLE IF NOT EXISTS monthly_revenue_report (
        location VARCHAR(255),
        category VARCHAR(50),
        bookings INT,
        travelers INT,
        revenue DECIMAL(12,2),
        avg_booking_value DECIMAL(12,2)
    );
    
    -- Cursor for revenue data by location and category
    DECLARE revenue_cursor CURSOR FOR
        SELECT 
            p.location,
            p.category,
            COUNT(b.booking_id) AS num_bookings,
            SUM(b.total_price) AS revenue,
            SUM(b.number_of_travelers) AS num_travelers
        FROM 
            bookings b
        JOIN 
            tour_schedules ts ON b.schedule_id = ts.schedule_id
        JOIN 
            package p ON ts.package_id = p.package_id
        WHERE 
            YEAR(b.booking_date) = report_year
            AND MONTH(b.booking_date) = report_month
            AND b.status IN ('confirmed', 'completed')
        GROUP BY 
            p.location, p.category;
            
    DECLARE CONTINUE HANDLER FOR NOT FOUND SET done = TRUE;
    
    -- Open cursor
    OPEN revenue_cursor;
    
    -- Start processing
    read_loop: LOOP
        FETCH revenue_cursor INTO tour_location, tour_category, booking_count, total_revenue, total_travelers;
        
        IF done THEN
            LEAVE read_loop;
        END IF;
        
        -- Insert data into temporary table
        INSERT INTO monthly_revenue_report (
            location, 
            category, 
            bookings, 
            travelers, 
            revenue, 
            avg_booking_value
        ) VALUES (
            tour_location,
            tour_category,
            booking_count,
            total_travelers,
            total_revenue,
            total_revenue / booking_count
        );
        
    END LOOP;
    
    CLOSE revenue_cursor;
    
    -- Return the report
    SELECT * FROM monthly_revenue_report
    ORDER BY revenue DESC;
    
    -- Add summary row
    SELECT 
        'TOTAL' AS location,
        '' AS category,
        SUM(bookings) AS bookings,
        SUM(travelers) AS travelers,
        SUM(revenue) AS revenue,
        SUM(revenue) / SUM(bookings) AS avg_booking_value
    FROM 
        monthly_revenue_report;
    
    -- Drop the temporary table
    DROP TEMPORARY TABLE IF EXISTS monthly_revenue_report;
END //

-- Reset delimiter
DELIMITER ;

```


## Step 9: Verify the Database

```sql
-- Verify tables were created
SHOW TABLES;

-- Verify data in tables
SELECT * FROM customers;
SELECT * FROM package;
SELECT * FROM hotels;
SELECT * FROM tour_schedules;
SELECT * FROM bookings;
SELECT * FROM payments;
SELECT * FROM feedback;
SELECT * FROM contact_details;

-- Verify views
SELECT * FROM active_tours;
SELECT * FROM customer_booking_history;
SELECT * FROM payment_summary;
SELECT * FROM popular_destinations;

-- Verify triggers
SHOW TRIGGERS;

-- Verify stored procedures
SHOW PROCEDURE STATUS WHERE Db = 'tourgrid_copy';
```

## Step 10: Test Triggers and Stored Procedures

```sql
-- Test update_booking_payment_status trigger
INSERT INTO payments (booking_id, amount, payment_date, payment_method, status, transaction_id)
VALUES ('BK006', 12000.00, CURDATE(), 'UPI', 'Completed', 'TRX011');

-- Check if payment status was updated
SELECT booking_id, payment_status FROM bookings WHERE booking_id = 'BK006';

-- Test customer_after_update trigger
UPDATE customers SET phone = '+91-9876543299' WHERE customer_id = 1;

-- Check if audit record was created
SELECT * FROM customer_audit;

-- Test process_pending_bookings procedure
CALL process_pending_bookings();

-- Check if pending bookings were processed
SELECT * FROM system_log;
SELECT booking_id, status FROM bookings WHERE booking_id = 'BK006';

-- Test generate_monthly_revenue_report procedure
CALL generate_monthly_revenue_report(2025, 3);
```









