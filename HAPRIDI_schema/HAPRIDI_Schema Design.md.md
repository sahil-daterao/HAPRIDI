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
Column Name              |              Data Type                 |                  Column Description
prison_id                |               INT (PK)                 |                  Unique ID
name                     |             VARCHAR(100)               |                  Name of prison
state                    |             VARCHAR(50)                |                  State location
capcity                  |                 INT                    |                  Total inmate capacity
prison_type              |             VARCHAR(50)                |                  Central, District, Sub-jail etc


# Table Name: inmate_details
## Purpose:
Column Name              |              Data Type                 |                  Column Description
inamte_id                |              INT (PK)                  |                  Unique prisoner ID
name                     |             VARCHAR(100)               |                  Name (anonymized)
age                      |                 INT                    |                  Age
gender                   |             VARCHAR(100)               |                  Male / Female
entry_date               |                 DATE                   |                  Entry into prison
prison_id                |               INT (FK)                 |                  Prison reference
crime_id                 |               INT (FK)                 |                  Crime reference
education_level          |             VATCHAR(50)                |                  Primary / Secondary / Graduation etc
previous_convictions     |                 INT                    |                  Repeat offender flag
skill_id                 |              INT (FK)                  |                  skill reference (if any)

# Table Name: crime_details
## Purpose:
Column Name              |             Data Type                  |                  Column Description
crime_id                 |              INT (PK)                  |                  crime Category
section_charged          |            VARCHAR (20)                |                  IPC section
crime_category           |            VARCHAR (50)                |                  Rioting, Theft, Murder, etc.
is_political             |              BOOLEAN                   |                  True if related to protest/unrest
bailable                 |              BOOLEAN                   |                  Bailable or not

# Table Name: political_events
## Purpose:
Column Name             |              Data Type                  |                  Description
event_id                |               INT (PK)                  |                  Event ID
event_name              |            VATCHAR(100)                 |                  Name of event
start_date              |                 DATE                    |                  Event start
end_date                |                 DATE                    |                  Event end
region                  |            VARCHAR(100)                 |                  Affected region
description             |                 TEXT                    |                  Notes


# Table Name:  inmates_skills_details
## Purpose:
Column Name             |              Data Type                  |                  Description
skill_id                |               INT (PK)                  |                  Skill ID
skill_name              |            VARCHAR(100)                 |                  Carpentary, Tailoring, Cooking, etc
is_utilized_inside_prison |             Boolean                   |                  Is the skill put to use inside?
earning_per_month       |            DECIMAL (10,2)               |                  Avg income if skill used

# Table Name: bail_cases
## Purpose:
Column Name             |             Data Type                   |                  Description
case_id                 |              INT (PK)                   |                  Bail case ID
inmate_id               |              INT (FK)                   |                  Reference
bail_applied            |              BOOLEAN                    |                  Applied for bail
bail_granted            |              BOOLEAN                    |                  Status 
legal_aid               |              BOOLEAN                    |                  Got legal aid
lawyer_type             |            VARCHAR(50)                  |                  Govt / Private / None

# Table Name: prison_transfers
## Purpose:
Column Name             |             Data Type                   |                  Description
transfer_id             |              INT (PK)                   |                  Unique transfer record
inmate_id               |              INT (FK)                   |                  Prisoner being moved
from_prison_id          |              INT (FK)                   |                  Source prison
transfer_date           |                DATE                     |                  Date of transfer
transfer_reason         |            VARCHAR(100)                 |                  Overcrowding / Security / Medical etc

# Table Name: event_related_incarcerations
## Purpose:
Column Name             |              Data Type                  |                  Description
incarceration_id        |           INT (Primary Key)             | 
inmate_id               |      INT (Foreign Key to inmates)       |
event_id                | INT (Foreign Key to political_events)   |
arrest_date             |                  DATE                   |
release_date            |                  DATE                   | 
crime_category          |              VARCHAR(100)               |
charges                 |                  TEXT                   |


# Table Name: inmate_devices 
## Purpose:
Column Name             |             Data Type                   |                  Description
device_id               |              INT (PK)                   |           Unique Device Identifier
inmate_id               |              INT (FK)                   |           linked to inmate_details
imei_number             |            VARCHAR(20)                  |             Device IMEI for Tracking
phone_number            |            VARCHAR(15)                  |                    SIM Number (If Known)
carrier                 |            VARCHAR(15)                  |             Mobile Network Provider
last_known_location     |            VARCHAR(100)                 |       Neares Tower/City or GPS if available
last_active_time        |             DATETIME                    |                 Time of Last Use
device_status           |            VARCHAR(20)                  |         'Confiscated', 'In-use', 'unknwon'


# Table Name: inamte_vehicles
## Purpose:
Column Name             |             Data Type                   |                  Description
vehicle_id              |           INT (PK)                      |           Unique Vehicle Identifier
inmate_id               |             INT (FK)                    |            linked to inamte_details
vehicle_type            |               VARCHAR(50)               |     Bike, Car, Truck, etc
registration_no         |           VARCHAR(20)                   | Vehicle Registration Number
make                    |               VARCHAR(50)               |     Manufacturer
model                   |               VARCHAR(50)               |            Vehicle Model
color                   |           VARCHAR(50)                   |        Color of the vehicle        
ownership_status        |            VARCHAR(50)                  |        Owned, Rented, Stolen, etc
location_last_seen      |           VARCHAR(100)                  |    Useful for investigation
incident_linked         |               VARCHAR(100)              |       Case/ Event ID (optional FK)

# Table Name: crime_scene_gps_logs
## Purpose: 
Column Name             |             Data Type                   |                  Description
log_id                  |           INT(PK)                       |                 Unique Log
inamte_id               |               INT(FK)                   |        Person whose location is tracked 
device_id               |           INT(FK)                       | Mobile or device
longitude               |       DECIMAL (9,6)                     | Longitude of device  
latitude                |           DECIMAL (9,6)                 | Latitude of device
timestamp               |           DATETIME                      |         Time of Reading
location_accuracy       |           VARCHAR(50)                   |     Tower, GPS, or WiFi accuracy

# Table Name: associated_camers
## Purpose:
Column Name             |             Data Type                   |                  Description
camera_id               | INT(PK)                                 | Unique Camera Identifier
location                | VARCHAR(100)                            | Installation location (e,,m ate 4, Cell Block B) 
camera_type             |       VARCHAR(50)                       | CCTC, Drone, Bodycam,etc
linked_event_id         |           INT(FK)                       | Optional link to political_events
linked_prison_id        |       INT(FK)                           | Optional link to a prison_master table
active_from             | DATE                                    | Date of Activitation
active_to               | DATE (nullable)                         | Date of decommission or NULL if active
status                  |        VARCHAR(20)                      | Active, Malfunctioning, Under Repair
storage_link            | VARCHAR(255)                            | File system or cloud reference to footable (if applicable)

# Table Name: panic_alerts
Column Name             |             Data Type                   |                  Description
alert_id                |           INT(PK)                       | Unique Alert
triggered_by_id         |               INT                       | Inmate or staff ID
role                    |  VATCHAR(10)                            | 'Inmate' or 'Staff'
location                | VARCHAR(100)                            | Where it was triggered
timestamp               | DATETIME                                | Time of trigger
resolved_by             | VARCHAR(100)                            | Officer who responded
resolved_time           | DATETIME                                | TIme of resolution


# Table Name: Inmate_mugshots
Column Name             |             Data Type                   |                  Description
mugshot_id              | INT(PK)                                 | Unique mugshot record
inamte_id               | INT(FK)                                 | FK to inmate_details
capture_date            | DATE                                    | Date when photo was taken
camera_id               | INT (FK)                                | FK to associated_cameras if same camera used
photo_url               | VARCHAR(255)                            | Link to stored image
photo_type              | VARCHAR(50)                             | 'Booking', 'Court Appearance', 'Release'
taken_by                | VARCHAR(100)                            | Officer/Staff name or ID
comments | TEXT                                                   | Optional notes (e.g., injury on face, haircut)


# Table Name: inmate_fingerprints
Column Name             |             Data Type                   |                  Description
fingerprint_id          | INT(PK)                                 | Unique record
inmate_id               | INT (FK)                                | FK to inmate_details
scan_date               | DATE                                    | When the finerprints were taken
device_id               | VARCHAR(50)                             | Scanner device reference
finger_position         | VARCHAR(50)                             | Left Thumb, Right Thumb, etc
quality_score           | DECIMAL(4,2)                            | Match / Readability score from 0-100
image_url               | VATCHAR(255)                            | Link to fingerprint image (if available)
collected_by            | VARCHAR(100)                            | Staff ID or name
comment                 | TEXT                                    | Notes on clarity, re-scan need, etc.