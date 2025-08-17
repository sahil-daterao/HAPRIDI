### HAPRIDI - Schema Design 
Help us derive insight like,
1. Population of prisoners spike, maintaining outmost detail to demography, and political events,
2. Skillset of Priosoners and provision of earning inside prisons based for the available skillset
3. We need to consider men and women prisoners as well for above two insights
4. Details for crime they've commited 
5. If possible bail details, availability of judiciary (lawyers) for wrongfully accused prisoners
6. Reason and no. of prisoners moving from one prison to other prison

### Database Name: HARPIDI

## SQL Schema Design 

## Table Name: prison_details
## Purpose: 
Column Name | Data Type | Constraint | Column Description
prison_id | INT (PK) | PK  | Unique ID
name  | VARCHAR(100) | NOT NULL, UNIQUE | Name of prison
state | VARCHAR(50) | NOT NULL | Geographic Location 
capcity | INT | NOT NULL CHECK (capacity >= 0) | Total inmate capacity
prison_type | VARCHAR(50) | NOT NULL CHECK (prisn_type in ('Central', 'District', 'Sub-jail', 'Open', 'Women' 'Other')) | Prison - Type
date_opened | DATE | NULL | Age_Based Analytics    
security_level | VARCHAR(50) | NOT NULL CHECK (security_level IN ("Low", "Medium", "High") | Low, medium, high)

Indexes: IX_prison_details_state (state)

# Table Name: inmate_details
## Purpose:
Column Name | Data Type | Constraints |  Column Description
inamte_id | INT (PK) | PK | Unique prisoner ID
name | VARCHAR(100)  | NOT NULL (anonymized) Name (anonymized)
age | INT | NOT NULL CHECK (age BETWEEN 12 and 110) Age
gender | VARCHAR(100) | NOT NULL CHECK (Gender IN ('Male', 'Female', 'Other')) Male / Female
entry_date | DATE | NOT NULL |Entry into prison 
prison_id | INT (FK) | NOT NULL, FK prison_details (prison_id) Prison reference
crime_id | INT (FK) | NOT NULL | Crime reference
education_level | VATCHAR(50) | NULL, CHECK (education_level IN ('primary', 'Secondary', 'Graduation', 'Postgrad', 'Other')) | Primary / Secondary / Graduation etc
previous_convictions | INT | NOT NULL, CHECK (Previous_convictions >=0) | Repeat offender flag
skill_id | INT (FK) | NULL | skill reference (if any)
admission_reason | VARCHAR(50) | NOT NULL CHECK ( admission_reason IN 'Convicted', 'Awaiting Trial') | 
Cell_block | VARCHAR(50) | NULL | For intra-prison grouping
status | VARCHAR(50) | NOT NULL, CHECK (status IN ('Active', 'Released', 'Graduation', 'Postgrad' 'Other')) | "Transfered", "Active", "Released"

Indexes: IX_inamte_prison_status (prison_id, status)
IX_inmate_enntry_date (entry_date)

# Table Name: crime_details
## Purpose: IPC/Offense catalog
Column Name | Data Type | Constraint | Description
crime_id | INT (PK) | PK | Crime Category
section_charged | VARCHAR (20) | NOT NULL | IPC section
crime_category | VARCHAR (50)  | NOT NULL | Rioting, Theft, Murder, etc.
is_political | BOOLEAN | NOT NULL | True if related to protest/unrest
bailable | BOOLEAN | NOT NULL | Bailable or not
severity_level | VARCHAR(50) | NOT NULL, CHECK (severity_level IN ('Minor', 'Moderate', 'Severity')) | 'Minor', 'Moderate', 'Severe'
date_of_Crime | DATE | Timeline based Analysis 
location_id ||| For Geo Analysis 

Unique/ Candidate Keys: (section_chared, crime_category)
Indexes: IX_crinmate_details_category (crime_categoury, severity_level)

# Table Name: political_events
## Purpose:
Column Name | Data Type |Constraint | Description
event_id | INT (PK) | PK | Event ID
event_name | VARCHAR(150)  | NOT NULL | Name of event
start_date | DATE | NOT NULL | Event start
end_date | DATE | NOT NULL, CHECK (end_date >= start_date) | Event end
region | VARCHAR(100) | NOT NULL | Affected region
description | VARCHAR(100)  | NULL | Notes
Event_duration_days | INT | NOT NULL, CHECK (event_duration_days >= 0) | Overallaping date range queries
impact_score | DECIMAL | NOT NULL, CHECK (impact_score BETWEEN 0 and 10)) | Ranking Queries

IX_events_dates (start_date, end_date)
IX_events_region (region)

# Table Name:  inmates_skills_details
## Purpose: Normalize Skills
Column Name | Data Type |Constraints | Description
skill_id | INT (PK)|PK, FK | Skill ID
skill_name | VARCHAR(100) |Not NULL, UNIQUE | Carpentary, Tailoring, Cooking, etc
is_utilized_inside_prison | Boolean |NOT NULL| Is the skill put to use inside?
earning_per_month | DECIMAL (12,2) |NULL, CHECK (earning_per_month >=0) | Avg income if skill used
Certification_obtained | Boolean |NOT NULL | For filtering
last_used_date | For time based queries |NULL | Time based queries

IX_inmate_skills_inamte (inmates_id)
IX_inmate_skills (skill_id)

# Table Name: bail_cases
## Purpose:
Column Name               | Data Type      | Constraints | Description
case_id                   | INT (PK)       | PK |Bail case ID
inmate_id                 | INT (FK)       | NOT NULL, FK -> inamte_details (inmate_id)  |Reference
bail_applied              | BOOLEAN        | NOT NULL | Applied for bail
bail_granted              | BOOLEAN        | NOT NULL | Status 
legal_aid                 | BOOLEAN        | NOT NULL | Got legal aid
lawyer_type               | VARCHAR(50)    | NOT NULL, CHECK (lawyer_type IN ('Govt','Private', 'None'))| Govt / Private / None 
bail_hearing_count        | INT            | NOT NULL, CHECK (bail_hearing_count >=0) | Numeric aggregation
Lawyer_id                 | INT            | NULL, FK -> lawyers_masters(lawyer_id) | Lawyers_Master           

IX_bail_inmate (inmate_id)

# Table Name: prison_transfers
## Purpose:
Column Name               | Data Type      | Constraints | Description
transfer_id               | INT (PK)       | PK | Unique transfer record
inmate_id                 | INT (FK)       | NOT NULL FK -> inamte_details (inmate_id) | Prisoner being moved
from_prison_id            | INT (FK)       | NOT NULL FK -> prison_details(prison_id) | Source prison
to_prison_id              | INT (FK)       | NOT NULL FK -> prison_deetails(prison_id) | Destination prison
transfer_date             | DATE           | NOT NULL | Date of transfer
transfer_reason           | VARCHAR(100)   | NOT NULL | Overcrowding / Security / Medical etc
trasnfer_status           |                | NOT NULL, CHECK (transfer_status IN ('Pending', 'Completed')) | "Pending", "Completed"

IX_transfer_inmate_date (inmate_id, transfer_date)

# Table Name: event_related_incarcerations
## Purpose:
Column Name               | Data Type      | CONSTRAINTS | Description
incarceration_id          | INT (Primary Key) |  PK                     |  
inmate_id                 | INT (Foreign Key to inmates) | NOT NULL, FK -> inmate_details(inmate_id) | inmate details            |
event_id                  | INT (Foreign Key to political_events) | NOT NULL, FK -> political_events(event_id) | 
arrest_date               | DATE           | NOT NULL | 
release_date              | DATE           | NULL, CHECK (release_date IS NULL OR release_date >== arrest_date) |
crime_category            | VARCHAR(100)   | NULL, CHECK (crime_category IN ('severere') |
charges                   | TEXT           | NULL | 
arrest_type               | VARCHAR        | NOT NULL, CHECK (arrest_type IN ('Preventive','Post-crime)) | "Preventive", "Completed"
processing_time_days      |                | NULL CHECK (processing_time_days >=0) | date calculations

IX_eri_event_inmate (event_id, inmate_id)
IX_eri_arrest_date (arrest_date)

# Table Name: inmate_devices 
## Purpose:
Column Name               | Data Type      | Description
device_id                 | INT (PK)       | Unique Device Identifier
inmate_id                 | INT (FK)       | linked to inmate_details
imei_number               | VARCHAR(20)    | Device IMEI for Tracking
phone_number              | VARCHAR(15)    | SIM Number (If Known)
carrier                   | VARCHAR(15)    | Mobile Network Provider
last_known_location       | VARCHAR(100)   | GPS if available
last_active_time          | DATETIME       | Time of Last Use
device_status             | VARCHAR(20)    | 'Confiscated', 'In-use', 'unknwon'
device_os                 |                |    

# Table Name: inamte_vehicles
## Purpose:
Column Name               | Data Type      | Description
vehicle_id                | INT (PK)       | Unique Vehicle Identifier
inmate_id                 | INT (FK)       | linked to inamte_details
vehicle_type              | VARCHAR(50)    | Bike, Car, Truck, etc
registration_no           | VARCHAR(20)    | Vehicle Registration Number
vehicle_make              | VARCHAR(50)    | Manufacturer
vehicle_model             | VARCHAR(50)    | Vehicle Model
vehicle_color             | VARCHAR(50)    | Color of the vehicle        
ownership_status          | VARCHAR(50)    | Owned, Rented, Stolen, etc
location_last_seen        | VARCHAR(100)   | Useful for investigation
incident_linked           | VARCHAR(100)   | Case/ Event ID (optional FK)
registration_state        | VARCHAR(100)   | Group by counts


# Table Name: crime_scene_gps_logs
## Purpose: 
Column Name               | Data Type      | Description
log_id                    | INT(PK)        | Unique Log
inamte_id                 | INT(FK)        | Person whose location is tracked 
device_id                 | INT(FK)        | Mobile or device
longitude                 | DECIMAL (9,6)  | Longitude of device  
latitude                  | DECIMAL (9,6)  | Latitude of device
timestamp                 | DATETIME       | Time of Reading
location_accuracy         | VARCHAR(50)    | Tower, GPS, or WiFi accuracy
location_type             | VARCHAR(50)    | "Urban", "Rural"
movement_speed            | Decimal        | Anomaly detection queries

# Table Name: associated_cameras
## Purpose:
Column Name               | Data Type      |                  Description
camera_id                 | INT(PK)        | Unique Camera Identifier
location                  | VARCHAR(100)   | Installation location (enmate, Cell Block B)  
camera_type               | VARCHAR(50)    | CCTC, Drone, Bodycam,etc
linked_event_id           | INT(FK)        | Optional link to political_events
linked_prison_id          | INT(FK)        | Optional link to a prison_master table
active_from               | DATE           | Date of Activitation
active_to                 | DATE (nullable)| Date of decommission or NULL if active
status                    | VARCHAR(20)    | Active, Malfunctioning, Under Repair
storage_link              | VARCHAR(255)   | File system or cloud reference to footable
camera_owner              |                | "Govt", "Private"
footage_format            |                | "Mp4", "AVI"
Server_Location           |                | 


# Table Name: panic_alerts
Column Name               |  Data Type   | Description
alert_id                  |  INT(PK)     | Unique Alert
triggered_by_id           |  INT         | Inmate or staff ID
role                      |  VATCHAR(10) | 'Inmate' or 'Staff'
location                  |  VARCHAR(100)| Where it was triggered
timestamp                 |  DATETIME    | Time of trigger
resolved_by               |  VARCHAR(100)| Officer who responded
resolved_time             |  DATETIME    | TIme of resolution
alert_severity            |              |
alert_type                |              |

# Table Name: Inmate_mugshots
Column Name               | Data Type    | Description
mugshot_id                | INT(PK)      | Unique mugshot record
inamte_id                 | INT(FK)      | FK to inmate_details
capture_date              | DATE         | Date when photo was taken
camera_id                 | INT (FK)     | FK to associated_cameras if same camera used
photo_url                 | VARCHAR(255) | Link to stored image
photo_type                | VARCHAR(50)  | 'Booking', 'Court Appearance', 'Release'
taken_by                  | VARCHAR(100) | Officer/Staff name or ID
comments                  | TEXT         | Optional notes (e.g., injury on face, haircut)


# Table Name: inmate_fingerprints
Column Name               | Data Type    | Description
fingerprint_id            | INT(PK)      | Unique record
inmate_id                 | INT (FK)     | FK to inmate_details
scan_date                 | DATE         | When the finerprints were taken
device_id                 | VARCHAR(50)  | Scanner device reference
finger_position           | VARCHAR(50)  | Left Thumb, Right Thumb, etc
quality_score             | DECIMAL(4,2) | Match / Readability score from 0-100
image_url                 | VATCHAR(255) | Link to fingerprint image (if available)
collected_by              | VARCHAR(100) | Staff ID or name
comment                   | TEXT         | Notes on clarity, re-scan need, etc.
scan_quality              | Decimal      | 
Is_Primary_Fingerprint    | Boolean      | 