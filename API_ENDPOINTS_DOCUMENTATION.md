# SIGECAP API Endpoints Documentation

## Table of Contents

1. [Attendances](#attendances)
2. [Course Participants](#course-participants)
3. [Courses](#courses)
4. [CSRF](#csrf)
5. [Documents](#documents)
6. [Evaluations](#evaluations)
7. [Google Sheets](#google-sheets)
8. [Login](#login)
9. [Permissions](#permissions)
10. [Roles](#roles)
11. [Training Management](#training-management)
12. [Training Request Statuses](#training-request-statuses)
13. [Training Request Workflow](#training-request-workflow)
14. [Training Requests](#training-requests)
15. [Users](#users)

---

## Attendances

### Overview
Manages attendance records for training courses and sessions.

### Endpoints

#### GET /attendances/
- **Description**: Retrieve attendance records with filtering options
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `course_id`: int (optional) - Filter by course ID
  - `user_id`: int (optional) - Filter by user ID
- **Response Schema**:
  - `id`: int
  - `user_id`: int
  - `course_id`: int
  - `session_date`: datetime
  - `status`: string
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /attendances/
- **Description**: Create a new attendance record
- **Authentication**: Required
- **Request Schema**:
  - `user_id`: int (required)
  - `course_id`: int (required)
  - `session_date`: datetime (required)
  - `status`: string (required)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /attendances/{attendance_id}
- **Description**: Retrieve a specific attendance record
- **Authentication**: Required
- **Path Parameters**:
  - `attendance_id`: int (required)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /attendances/{attendance_id}
- **Description**: Update an existing attendance record
- **Authentication**: Required
- **Path Parameters**:
  - `attendance_id`: int (required)
- **Request Schema**: Same as POST request
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /attendances/{attendance_id}
- **Description**: Delete an attendance record
- **Authentication**: Required
- **Path Parameters**:
  - `attendance_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

### Related Files
- **CRUD**: `/crud/attendances.py`
- **Models**: `/models/attendance.py`
- **Schemas**: `/schemas/attendance.py`

### Dependencies
- Uses SQLAlchemy ORM for database operations
- Integrates with user and course models for relationship validation
- Implements role-based access control for attendance management

---

// ...existing content...

## Course Participants

### Overview
Manages participant enrollment and participation in training courses.

### Endpoints

#### GET /course-participants/
- **Description**: Retrieve course participants with filtering options
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `course_id`: int (optional) - Filter by course ID
  - `user_id`: int (optional) - Filter by user ID
  - `status`: string (optional) - Filter by participation status
- **Response Schema**:
  - `id`: int
  - `user_id`: int
  - `course_id`: int
  - `enrollment_date`: datetime
  - `status`: string
  - `completion_date`: datetime (nullable)
  - `certificate_issued`: boolean
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /course-participants/
- **Description**: Enroll a participant in a course
- **Authentication**: Required
- **Request Schema**:
  - `user_id`: int (required)
  - `course_id`: int (required)
  - `enrollment_date`: datetime (optional, defaults to current time)
  - `status`: string (default: "enrolled")
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (already enrolled, course full, etc.)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /course-participants/{participant_id}
- **Description**: Retrieve a specific course participant record
- **Authentication**: Required
- **Path Parameters**:
  - `participant_id`: int (required)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /course-participants/{participant_id}
- **Description**: Update a course participant record
- **Authentication**: Required
- **Path Parameters**:
  - `participant_id`: int (required)
- **Request Schema**:
  - `status`: string (optional)
  - `completion_date`: datetime (optional)
  - `certificate_issued`: boolean (optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /course-participants/{participant_id}
- **Description**: Remove a participant from a course
- **Authentication**: Required
- **Path Parameters**:
  - `participant_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /course-participants/bulk-enroll
- **Description**: Enroll multiple participants in a course
- **Authentication**: Required (Admin/Instructor only)
- **Request Schema**:
  - `course_id`: int (required)
  - `user_ids`: list[int] (required)
- **Response Schema**:
  - `enrolled`: list[int] - Successfully enrolled user IDs
  - `failed`: list[dict] - Failed enrollments with reasons
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/course_participants.py`
- **Models**: `/models/course_participant.py`
- **Schemas**: `/schemas/course_participant.py`

### Dependencies
- Integrates with Course and User models for validation
- Uses enrollment capacity checking from Course model
- Implements certificate generation workflow
- Connects with attendance tracking system

---


## Courses

### Overview
Manages training courses, their metadata, scheduling, and capacity management.

### Endpoints

#### GET /courses/
- **Description**: Retrieve courses with filtering and search options
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `search`: string (optional) - Search by title or description
  - `category`: string (optional) - Filter by course category
  - `status`: string (optional) - Filter by course status
  - `instructor_id`: int (optional) - Filter by instructor
  - `start_date_from`: datetime (optional) - Filter courses starting from date
  - `start_date_to`: datetime (optional) - Filter courses starting until date
- **Response Schema**:
  - `id`: int
  - `title`: string
  - `description`: string
  - `category`: string
  - `instructor_id`: int
  - `instructor_name`: string
  - `start_date`: datetime
  - `end_date`: datetime
  - `duration_hours`: int
  - `max_participants`: int
  - `current_participants`: int
  - `status`: string
  - `location`: string
  - `modality`: string
  - `requirements`: string
  - `objectives`: string
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 422: Validation Error

#### POST /courses/
- **Description**: Create a new course
- **Authentication**: Required (Admin/Instructor only)
- **Request Schema**:
  - `title`: string (required)
  - `description`: string (required)
  - `category`: string (required)
  - `instructor_id`: int (required)
  - `start_date`: datetime (required)
  - `end_date`: datetime (required)
  - `duration_hours`: int (required)
  - `max_participants`: int (required)
  - `location`: string (optional)
  - `modality`: string (required) - "presential", "virtual", "hybrid"
  - `requirements`: string (optional)
  - `objectives`: string (optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /courses/{course_id}
- **Description**: Retrieve a specific course with detailed information
- **Authentication**: Required
- **Path Parameters**:
  - `course_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `participants`: list[dict] - List of enrolled participants
  - `sessions`: list[dict] - Course sessions information
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 404: Not Found

#### PUT /courses/{course_id}
- **Description**: Update an existing course
- **Authentication**: Required (Admin/Course Instructor only)
- **Path Parameters**:
  - `course_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /courses/{course_id}
- **Description**: Delete a course (only if no participants enrolled)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `course_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (participants enrolled)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /courses/{course_id}/participants
- **Description**: Get all participants for a specific course
- **Authentication**: Required
- **Path Parameters**:
  - `course_id`: int (required)
- **Response Schema**:
  - `participants`: list[dict] with user and participation details
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /courses/{course_id}/duplicate
- **Description**: Create a copy of an existing course
- **Authentication**: Required (Admin/Instructor only)
- **Path Parameters**:
  - `course_id`: int (required)
- **Request Schema**:
  - `title`: string (optional) - New title for duplicated course
  - `start_date`: datetime (required) - New start date
  - `end_date`: datetime (required) - New end date
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

### Related Files
- **CRUD**: `/crud/courses.py`
- **Models**: `/models/course.py`
- **Schemas**: `/schemas/course.py`

### Dependencies
- Integrates with User model for instructor validation
- Connects with CourseParticipant model for enrollment management
- Uses Attendance model for session tracking
- Implements capacity management and scheduling validation
- Integrates with training request workflow for course assignment

---

// ...existing content...

## CSRF

### Overview
Provides Cross-Site Request Forgery protection tokens for secure form submissions.

### Endpoints

#### GET /csrf/token
- **Description**: Generate and retrieve a CSRF token for form protection
- **Authentication**: Not required (but session-based)
- **Query Parameters**: None
- **Response Schema**:
  - `csrf_token`: string - The generated CSRF token
  - `expires_at`: datetime - Token expiration time
- **Status Codes**:
  - 200: Success
  - 500: Internal Server Error

#### POST /csrf/validate
- **Description**: Validate a CSRF token
- **Authentication**: Not required
- **Request Schema**:
  - `csrf_token`: string (required) - Token to validate
- **Response Schema**:
  - `valid`: boolean - Whether the token is valid
  - `message`: string - Validation result message
- **Status Codes**:
  - 200: Success (valid or invalid token)
  - 400: Bad Request (missing token)
  - 422: Validation Error

### Related Files
- **CRUD**: Not applicable (utility endpoint)
- **Models**: Not applicable
- **Schemas**: `/schemas/csrf.py`

### Dependencies
- Uses session management for token storage
- Implements time-based token expiration
- Integrates with form submission endpoints for security

---

## Documents

### Overview
Manages document uploads, downloads, and metadata for training materials and certificates.

### Endpoints

#### GET /documents/
- **Description**: Retrieve documents with filtering options
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `document_type`: string (optional) - Filter by document type
  - `course_id`: int (optional) - Filter by associated course
  - `user_id`: int (optional) - Filter by document owner
  - `status`: string (optional) - Filter by document status
- **Response Schema**:
  - `id`: int
  - `filename`: string
  - `original_filename`: string
  - `file_path`: string
  - `file_size`: int
  - `mime_type`: string
  - `document_type`: string
  - `course_id`: int (nullable)
  - `user_id`: int
  - `status`: string
  - `upload_date`: datetime
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /documents/upload
- **Description**: Upload a new document
- **Authentication**: Required
- **Request Schema** (multipart/form-data):
  - `file`: file (required) - The document file
  - `document_type`: string (required) - Type of document
  - `course_id`: int (optional) - Associated course ID
  - `description`: string (optional) - Document description
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (file too large, invalid type)
  - 401: Unauthorized
  - 403: Forbidden
  - 413: Payload Too Large
  - 422: Validation Error

#### GET /documents/{document_id}
- **Description**: Retrieve document metadata
- **Authentication**: Required
- **Path Parameters**:
  - `document_id`: int (required)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /documents/{document_id}/download
- **Description**: Download a document file
- **Authentication**: Required
- **Path Parameters**:
  - `document_id`: int (required)
- **Response**: File stream with appropriate headers
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /documents/{document_id}
- **Description**: Update document metadata
- **Authentication**: Required (Owner/Admin only)
- **Path Parameters**:
  - `document_id`: int (required)
- **Request Schema**:
  - `document_type`: string (optional)
  - `description`: string (optional)
  - `status`: string (optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /documents/{document_id}
- **Description**: Delete a document and its file
- **Authentication**: Required (Owner/Admin only)
- **Path Parameters**:
  - `document_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /documents/generate-certificate
- **Description**: Generate a completion certificate for a course participant
- **Authentication**: Required (Admin/Instructor only)
- **Request Schema**:
  - `course_id`: int (required)
  - `user_id`: int (required)
  - `template_id`: int (optional) - Certificate template to use
- **Response Schema**: Same as GET response for the generated certificate
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (participant not completed)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/documents.py`
- **Models**: `/models/document.py`
- **Schemas**: `/schemas/document.py`

### Dependencies
- Integrates with file storage system for document management
- Uses Course and User models for access control
- Implements certificate generation with templates
- Handles file type validation and size limits
- Connects with training completion workflow

---

// ...existing content...

## Evaluations

### Overview
Manages course evaluations, feedback collection, and assessment scoring for training programs.

### Endpoints

#### GET /evaluations/
- **Description**: Retrieve evaluations with filtering options
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `course_id`: int (optional) - Filter by course ID
  - `evaluator_id`: int (optional) - Filter by evaluator user ID
  - `evaluation_type`: string (optional) - Filter by evaluation type
  - `status`: string (optional) - Filter by evaluation status
- **Response Schema**:
  - `id`: int
  - `course_id`: int
  - `evaluator_id`: int
  - `evaluation_type`: string
  - `title`: string
  - `description`: string
  - `questions`: list[dict]
  - `responses`: list[dict]
  - `score`: float (nullable)
  - `status`: string
  - `submitted_at`: datetime (nullable)
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /evaluations/
- **Description**: Create a new evaluation form
- **Authentication**: Required (Admin/Instructor only)
- **Request Schema**:
  - `course_id`: int (required)
  - `evaluation_type`: string (required) - "course", "instructor", "self"
  - `title`: string (required)
  - `description`: string (optional)
  - `questions`: list[dict] (required) - Evaluation questions
  - `is_mandatory`: boolean (default: false)
  - `deadline`: datetime (optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /evaluations/{evaluation_id}
- **Description**: Retrieve a specific evaluation
- **Authentication**: Required
- **Path Parameters**:
  - `evaluation_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `statistics`: dict - Response statistics if user has access
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /evaluations/{evaluation_id}
- **Description**: Update an evaluation form (before responses are submitted)
- **Authentication**: Required (Admin/Creator only)
- **Path Parameters**:
  - `evaluation_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (responses already submitted)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /evaluations/{evaluation_id}
- **Description**: Delete an evaluation (only if no responses submitted)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `evaluation_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (responses exist)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /evaluations/{evaluation_id}/respond
- **Description**: Submit a response to an evaluation
- **Authentication**: Required
- **Path Parameters**:
  - `evaluation_id`: int (required)
- **Request Schema**:
  - `responses`: list[dict] (required) - Question responses
  - `additional_comments`: string (optional)
- **Response Schema**:
  - `id`: int
  - `evaluation_id`: int
  - `respondent_id`: int
  - `responses`: list[dict]
  - `score`: float (nullable)
  - `submitted_at`: datetime
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (already responded, deadline passed)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### GET /evaluations/{evaluation_id}/responses
- **Description**: Get all responses for an evaluation
- **Authentication**: Required (Admin/Instructor only)
- **Path Parameters**:
  - `evaluation_id`: int (required)
- **Query Parameters**:
  - `include_anonymous`: boolean (default: true) - Include anonymous responses
- **Response Schema**:
  - `responses`: list[dict] - All evaluation responses
  - `statistics`: dict - Response statistics and analytics
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /evaluations/{evaluation_id}/export
- **Description**: Export evaluation responses to CSV/Excel
- **Authentication**: Required (Admin/Instructor only)
- **Path Parameters**:
  - `evaluation_id`: int (required)
- **Query Parameters**:
  - `format`: string (default: "csv") - Export format: "csv" or "excel"
- **Response**: File download
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

### Related Files
- **CRUD**: `/crud/evaluations.py`
- **Models**: `/models/evaluation.py`, `/models/evaluation_response.py`
- **Schemas**: `/schemas/evaluation.py`, `/schemas/evaluation_response.py`

### Dependencies
- Integrates with Course model for evaluation assignment
- Uses User model for evaluator and respondent management
- Implements statistical analysis for response aggregation
- Connects with notification system for evaluation reminders
- Supports multiple question types (rating, multiple choice, text)

---

// ...existing content...

## Google Sheets

### Overview
Manages integration with Google Sheets for data import/export, reporting, and synchronization with external systems.

### Endpoints

#### GET /google-sheets/
- **Description**: Retrieve configured Google Sheets integrations
- **Authentication**: Required (Admin only)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `status`: string (optional) - Filter by integration status
- **Response Schema**:
  - `id`: int
  - `sheet_id`: string
  - `sheet_name`: string
  - `integration_type`: string
  - `sync_frequency`: string
  - `last_sync`: datetime (nullable)
  - `status`: string
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /google-sheets/connect
- **Description**: Create a new Google Sheets integration
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `sheet_id`: string (required) - Google Sheets ID
  - `sheet_name`: string (required) - Display name for integration
  - `integration_type`: string (required) - "import", "export", "sync"
  - `sync_frequency`: string (optional) - "manual", "daily", "weekly"
  - `config`: dict (required) - Integration configuration
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (invalid sheet ID, access denied)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /google-sheets/{integration_id}
- **Description**: Retrieve a specific Google Sheets integration
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `integration_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `config`: dict - Full integration configuration
  - `sync_history`: list[dict] - Recent synchronization history
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /google-sheets/{integration_id}
- **Description**: Update a Google Sheets integration
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `integration_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /google-sheets/{integration_id}
- **Description**: Remove a Google Sheets integration
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `integration_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /google-sheets/{integration_id}/sync
- **Description**: Manually trigger synchronization with Google Sheets
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `integration_id`: int (required)
- **Request Schema**:
  - `force`: boolean (default: false) - Force sync even if recently synced
- **Response Schema**:
  - `sync_id`: string
  - `status`: string
  - `records_processed`: int
  - `started_at`: datetime
- **Status Codes**:
  - 202: Accepted (sync started)
  - 400: Bad Request (sync already in progress)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /google-sheets/export/courses
- **Description**: Export courses data to a Google Sheet
- **Authentication**: Required (Admin/Instructor only)
- **Request Schema**:
  - `sheet_id`: string (required) - Target Google Sheets ID
  - `sheet_name`: string (optional) - Worksheet name
  - `filters`: dict (optional) - Course filters to apply
  - `include_participants`: boolean (default: false)
- **Response Schema**:
  - `export_id`: string
  - `sheet_url`: string
  - `records_exported`: int
  - `exported_at`: datetime
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /google-sheets/export/participants
- **Description**: Export participants data to a Google Sheet
- **Authentication**: Required (Admin/Instructor only)
- **Request Schema**:
  - `sheet_id`: string (required)
  - `course_id`: int (optional) - Export participants for specific course
  - `include_attendance`: boolean (default: false)
  - `include_evaluations`: boolean (default: false)
- **Response Schema**: Same as export/courses response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /google-sheets/import/users
- **Description**: Import users from a Google Sheet
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `sheet_id`: string (required)
  - `sheet_range`: string (required) - Range to import (e.g., "A1:F100")
  - `mapping`: dict (required) - Column mapping configuration
  - `update_existing`: boolean (default: false)
- **Response Schema**:
  - `import_id`: string
  - `status`: string
  - `records_imported`: int
  - `records_updated`: int
  - `records_failed`: int
  - `errors`: list[dict]
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /google-sheets/sync-status/{sync_id}
- **Description**: Check the status of a synchronization operation
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `sync_id`: string (required)
- **Response Schema**:
  - `sync_id`: string
  - `status`: string
  - `progress`: int (0-100)
  - `records_processed`: int
  - `started_at`: datetime
  - `completed_at`: datetime (nullable)
  - `errors`: list[dict]
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

### Related Files
- **CRUD**: `/crud/google_sheets.py`
- **Models**: `/models/google_integration.py`
- **Schemas**: `/schemas/google_sheets.py`

### Dependencies
- Uses Google Sheets API for data operations
- Integrates with Google OAuth2 for authentication
- Connects with User, Course, and Participant models for data mapping
- Implements background task processing for large operations
- Uses Redis/Celery for async synchronization tasks

---

// ...existing content...

## Login

### Overview
Handles user authentication, session management, and authorization token generation.

### Endpoints

#### POST /login/
- **Description**: Authenticate user credentials and create session
- **Authentication**: Not required
- **Request Schema**:
  - `username`: string (required) - Username or email
  - `password`: string (required) - User password
  - `remember_me`: boolean (default: false) - Extended session duration
- **Response Schema**:
  - `access_token`: string - JWT access token
  - `refresh_token`: string - JWT refresh token
  - `token_type`: string - "bearer"
  - `expires_in`: int - Token expiration in seconds
  - `user`: dict - User profile information
    - `id`: int
    - `username`: string
    - `email`: string
    - `full_name`: string
    - `role`: string
    - `is_active`: boolean
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (invalid credentials)
  - 401: Unauthorized (account locked, inactive)
  - 422: Validation Error

#### POST /login/refresh
- **Description**: Refresh access token using refresh token
- **Authentication**: Required (refresh token)
- **Request Schema**:
  - `refresh_token`: string (required) - Valid refresh token
- **Response Schema**: Same as login response
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized (invalid/expired refresh token)
  - 422: Validation Error

#### POST /login/logout
- **Description**: Invalidate current session and tokens
- **Authentication**: Required
- **Request Schema**: None (uses authorization header)
- **Response Schema**:
  - `message`: string - Logout confirmation
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized

#### POST /login/logout-all
- **Description**: Invalidate all user sessions across devices
- **Authentication**: Required
- **Request Schema**: None
- **Response Schema**:
  - `message`: string - Logout confirmation
  - `sessions_invalidated`: int - Number of sessions terminated
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized

#### POST /login/forgot-password
- **Description**: Request password reset email
- **Authentication**: Not required
- **Request Schema**:
  - `email`: string (required) - User email address
- **Response Schema**:
  - `message`: string - Confirmation message
- **Status Codes**:
  - 200: Success (always returns success for security)
  - 422: Validation Error

#### POST /login/reset-password
- **Description**: Reset password using reset token
- **Authentication**: Not required
- **Request Schema**:
  - `token`: string (required) - Password reset token
  - `new_password`: string (required) - New password
  - `confirm_password`: string (required) - Password confirmation
- **Response Schema**:
  - `message`: string - Success confirmation
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (invalid token, passwords don't match)
  - 422: Validation Error

#### POST /login/change-password
- **Description**: Change password for authenticated user
- **Authentication**: Required
- **Request Schema**:
  - `current_password`: string (required) - Current password
  - `new_password`: string (required) - New password
  - `confirm_password`: string (required) - Password confirmation
- **Response Schema**:
  - `message`: string - Success confirmation
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (incorrect current password, passwords don't match)
  - 401: Unauthorized
  - 422: Validation Error

#### GET /login/verify-token
- **Description**: Verify if current token is valid
- **Authentication**: Required
- **Request Schema**: None
- **Response Schema**:
  - `valid`: boolean - Token validity
  - `user`: dict - User information if valid
  - `expires_at`: datetime - Token expiration time
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized (invalid token)

#### POST /login/two-factor/enable
- **Description**: Enable two-factor authentication
- **Authentication**: Required
- **Request Schema**:
  - `verification_code`: string (required) - TOTP code from authenticator app
- **Response Schema**:
  - `backup_codes`: list[string] - One-time backup codes
  - `qr_code`: string - QR code for authenticator setup
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (invalid verification code)
  - 401: Unauthorized
  - 422: Validation Error

#### POST /login/two-factor/disable
- **Description**: Disable two-factor authentication
- **Authentication**: Required
- **Request Schema**:
  - `verification_code`: string (required) - TOTP code or backup code
- **Response Schema**:
  - `message`: string - Success confirmation
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (invalid verification code)
  - 401: Unauthorized
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/users.py`, `/crud/auth_tokens.py`
- **Models**: `/models/user.py`, `/models/auth_token.py`
- **Schemas**: `/schemas/auth.py`, `/schemas/user.py`

### Dependencies
- Uses JWT for token-based authentication
- Integrates with password hashing (bcrypt/argon2)
- Implements rate limiting for login attempts
- Uses email service for password reset notifications
- Supports TOTP for two-factor authentication
- Connects with session management for security tracking

---

// ...existing content...

## Permissions

### Overview
Manages user permissions, access control, and authorization rules for different system resources and actions.

### Endpoints

#### GET /permissions/
- **Description**: Retrieve all available permissions in the system
- **Authentication**: Required (Admin only)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `resource`: string (optional) - Filter by resource type
  - `action`: string (optional) - Filter by action type
- **Response Schema**:
  - `id`: int
  - `name`: string
  - `codename`: string
  - `resource`: string
  - `action`: string
  - `description`: string
  - `is_system`: boolean
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /permissions/
- **Description**: Create a new custom permission
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `name`: string (required) - Human-readable permission name
  - `codename`: string (required) - Unique permission identifier
  - `resource`: string (required) - Resource type (e.g., "course", "user")
  - `action`: string (required) - Action type (e.g., "create", "read", "update", "delete")
  - `description`: string (optional) - Permission description
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (duplicate codename)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /permissions/{permission_id}
- **Description**: Retrieve a specific permission
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `permission_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `assigned_roles`: list[dict] - Roles that have this permission
  - `assigned_users`: list[dict] - Users with direct permission assignment
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /permissions/{permission_id}
- **Description**: Update an existing permission (non-system only)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `permission_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (cannot modify system permissions)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /permissions/{permission_id}
- **Description**: Delete a custom permission (non-system only)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `permission_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (cannot delete system permissions, permission in use)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /permissions/user/{user_id}
- **Description**: Get all permissions for a specific user
- **Authentication**: Required (Admin or self)
- **Path Parameters**:
  - `user_id`: int (required)
- **Response Schema**:
  - `user_id`: int
  - `direct_permissions`: list[dict] - Permissions assigned directly to user
  - `role_permissions`: list[dict] - Permissions inherited from roles
  - `effective_permissions`: list[dict] - All permissions user has access to
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /permissions/user/{user_id}/assign
- **Description**: Assign permissions directly to a user
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `user_id`: int (required)
- **Request Schema**:
  - `permission_ids`: list[int] (required) - List of permission IDs to assign
- **Response Schema**:
  - `assigned`: list[int] - Successfully assigned permission IDs
  - `already_assigned`: list[int] - Permission IDs already assigned
  - `invalid`: list[int] - Invalid permission IDs
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /permissions/user/{user_id}/revoke
- **Description**: Revoke permissions from a user
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `user_id`: int (required)
- **Request Schema**:
  - `permission_ids`: list[int] (required) - List of permission IDs to revoke
- **Response Schema**:
  - `revoked`: list[int] - Successfully revoked permission IDs
  - `not_assigned`: list[int] - Permission IDs that weren't assigned
  - `invalid`: list[int] - Invalid permission IDs
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### POST /permissions/check
- **Description**: Check if current user has specific permissions
- **Authentication**: Required
- **Request Schema**:
  - `permissions`: list[string] (required) - List of permission codenames to check
  - `resource_id`: int (optional) - Specific resource ID for object-level permissions
- **Response Schema**:
  - `permissions`: dict - Permission codename -> boolean mapping
  - `has_all`: boolean - Whether user has all requested permissions
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 422: Validation Error

#### GET /permissions/resources
- **Description**: Get available resource types and their actions
- **Authentication**: Required (Admin only)
- **Response Schema**:
  - `resources`: dict - Resource type -> available actions mapping
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden

### Related Files
- **CRUD**: `/crud/permissions.py`, `/crud/user_permissions.py`
- **Models**: `/models/permission.py`, `/models/user_permission.py`
- **Schemas**: `/schemas/permission.py`

### Dependencies
- Integrates with Role model for role-based permissions
- Uses User model for direct permission assignment
- Implements hierarchical permission checking
- Supports object-level permissions for specific resources
- Connects with all protected endpoints for authorization

---

// ...existing content...

## Roles

### Overview
Manages user roles, role hierarchies, and role-based permission assignments for system access control.

### Endpoints

#### GET /roles/
- **Description**: Retrieve all roles in the system
- **Authentication**: Required (Admin/HR only)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `is_active`: boolean (optional) - Filter by role status
  - `level`: int (optional) - Filter by role hierarchy level
- **Response Schema**:
  - `id`: int
  - `name`: string
  - `description`: string
  - `level`: int
  - `is_active`: boolean
  - `is_system`: boolean
  - `permissions_count`: int
  - `users_count`: int
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /roles/
- **Description**: Create a new role
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `name`: string (required) - Role name
  - `description`: string (optional) - Role description
  - `level`: int (required) - Hierarchy level (1-10)
  - `permissions`: list[int] (optional) - Initial permission IDs
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (duplicate name, invalid level)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /roles/{role_id}
- **Description**: Retrieve a specific role with detailed information
- **Authentication**: Required (Admin/HR only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `permissions`: list[dict] - Assigned permissions
  - `users`: list[dict] - Users with this role
  - `parent_roles`: list[dict] - Higher level roles
  - `child_roles`: list[dict] - Lower level roles
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /roles/{role_id}
- **Description**: Update an existing role (non-system only)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (cannot modify system roles)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /roles/{role_id}
- **Description**: Delete a role (non-system only, no users assigned)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (system role, users assigned)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /roles/{role_id}/permissions
- **Description**: Get all permissions assigned to a role
- **Authentication**: Required (Admin/HR only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Response Schema**:
  - `role_id`: int
  - `permissions`: list[dict] - Permission details
  - `inherited_permissions`: list[dict] - Permissions from parent roles
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /roles/{role_id}/permissions
- **Description**: Assign permissions to a role
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Request Schema**:
  - `permission_ids`: list[int] (required) - Permission IDs to assign
- **Response Schema**:
  - `assigned`: list[int] - Successfully assigned permission IDs
  - `already_assigned`: list[int] - Already assigned permission IDs
  - `invalid`: list[int] - Invalid permission IDs
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /roles/{role_id}/permissions
- **Description**: Remove permissions from a role
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Request Schema**:
  - `permission_ids`: list[int] (required) - Permission IDs to remove
- **Response Schema**:
  - `removed`: list[int] - Successfully removed permission IDs
  - `not_assigned`: list[int] - Permission IDs that weren't assigned
  - `invalid`: list[int] - Invalid permission IDs
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### GET /roles/{role_id}/users
- **Description**: Get all users assigned to a role
- **Authentication**: Required (Admin/HR only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Query Parameters**:
  - `is_active`: boolean (optional) - Filter by user status
- **Response Schema**:
  - `role_id`: int
  - `users`: list[dict] - User details with assignment date
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /roles/{role_id}/users
- **Description**: Assign users to a role
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Request Schema**:
  - `user_ids`: list[int] (required) - User IDs to assign
- **Response Schema**:
  - `assigned`: list[int] - Successfully assigned user IDs
  - `already_assigned`: list[int] - Already assigned user IDs
  - `invalid`: list[int] - Invalid user IDs
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /roles/{role_id}/users
- **Description**: Remove users from a role
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `role_id`: int (required)
- **Request Schema**:
  - `user_ids`: list[int] (required) - User IDs to remove
- **Response Schema**:
  - `removed`: list[int] - Successfully removed user IDs
  - `not_assigned`: list[int] - User IDs that weren't assigned
  - `invalid`: list[int] - Invalid user IDs
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### GET /roles/hierarchy
- **Description**: Get the complete role hierarchy
- **Authentication**: Required (Admin/HR only)
- **Response Schema**:
  - `hierarchy`: list[dict] - Nested role hierarchy structure
  - `levels`: dict - Level number -> roles mapping
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden

### Related Files
- **CRUD**: `/crud/roles.py`, `/crud/user_roles.py`
- **Models**: `/models/role.py`, `/models/user_role.py`
- **Schemas**: `/schemas/role.py`

### Dependencies
- Integrates with Permission model for role-based access control
- Uses User model for role assignment
- Implements hierarchical role inheritance
- Connects with authentication system for authorization
- Supports role-based navigation and feature access

---

// ...existing content...

## Training Management

### Overview
Manages comprehensive training program administration, including program creation, scheduling, resource allocation, and progress tracking.

### Endpoints

#### GET /training-management/programs/
- **Description**: Retrieve training programs with filtering options
- **Authentication**: Required (Admin/Training Manager only)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `status`: string (optional) - Filter by program status
  - `department`: string (optional) - Filter by target department
  - `year`: int (optional) - Filter by program year
  - `manager_id`: int (optional) - Filter by training manager
- **Response Schema**:
  - `id`: int
  - `name`: string
  - `description`: string
  - `department`: string
  - `manager_id`: int
  - `manager_name`: string
  - `start_date`: date
  - `end_date`: date
  - `budget`: float
  - `status`: string
  - `courses_count`: int
  - `participants_count`: int
  - `completion_rate`: float
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /training-management/programs/
- **Description**: Create a new training program
- **Authentication**: Required (Admin/Training Manager only)
- **Request Schema**:
  - `name`: string (required) - Program name
  - `description`: string (required) - Program description
  - `department`: string (required) - Target department
  - `manager_id`: int (required) - Training manager ID
  - `start_date`: date (required) - Program start date
  - `end_date`: date (required) - Program end date
  - `budget`: float (optional) - Program budget
  - `objectives`: list[string] (optional) - Program objectives
  - `target_audience`: string (optional) - Target audience description
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-management/programs/{program_id}
- **Description**: Retrieve detailed information about a training program
- **Authentication**: Required (Admin/Training Manager/Participant)
- **Path Parameters**:
  - `program_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `objectives`: list[string] - Program objectives
  - `target_audience`: string - Target audience
  - `courses`: list[dict] - Associated courses
  - `participants`: list[dict] - Program participants
  - `progress_metrics`: dict - Progress and completion metrics
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /training-management/programs/{program_id}
- **Description**: Update a training program
- **Authentication**: Required (Admin/Program Manager only)
- **Path Parameters**:
  - `program_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /training-management/programs/{program_id}
- **Description**: Delete a training program (only if no courses assigned)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `program_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (courses assigned)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /training-management/calendar/
- **Description**: Get training calendar view with all scheduled activities
- **Authentication**: Required
- **Query Parameters**:
  - `start_date`: date (required) - Calendar start date
  - `end_date`: date (required) - Calendar end date
  - `view`: string (default: "month") - Calendar view: "week", "month", "year"
  - `department`: string (optional) - Filter by department
  - `user_id`: int (optional) - Filter for specific user
- **Response Schema**:
  - `events`: list[dict] - Calendar events
    - `id`: int
    - `title`: string
    - `type`: string - "course", "session", "evaluation"
    - `start_datetime`: datetime
    - `end_datetime`: datetime
    - `location`: string
    - `instructor`: string
    - `participants_count`: int
  - `summary`: dict - Calendar period summary
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 422: Validation Error

#### GET /training-management/dashboard/
- **Description**: Get training management dashboard metrics
- **Authentication**: Required (Admin/Training Manager only)
- **Query Parameters**:
  - `period`: string (default: "month") - Metrics period: "week", "month", "quarter", "year"
  - `department`: string (optional) - Filter by department
- **Response Schema**:
  - `overview`: dict - Key metrics overview
    - `total_programs`: int
    - `active_courses`: int
    - `total_participants`: int
    - `completion_rate`: float
  - `trends`: dict - Trend data for charts
  - `top_courses`: list[dict] - Most popular courses
  - `upcoming_deadlines`: list[dict] - Upcoming training deadlines
  - `budget_utilization`: dict - Budget usage metrics
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /training-management/bulk-assign/
- **Description**: Bulk assign participants to training programs
- **Authentication**: Required (Admin/Training Manager only)
- **Request Schema**:
  - `program_id`: int (required) - Target program ID
  - `user_ids`: list[int] (required) - List of user IDs to assign
  - `notify_participants`: boolean (default: true) - Send notification emails
- **Response Schema**:
  - `assigned`: list[int] - Successfully assigned user IDs
  - `already_assigned`: list[int] - Already assigned user IDs
  - `invalid`: list[int] - Invalid user IDs
  - `failed`: list[dict] - Failed assignments with reasons
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-management/reports/completion
- **Description**: Generate training completion reports
- **Authentication**: Required (Admin/Training Manager only)
- **Query Parameters**:
  - `program_id`: int (optional) - Filter by program
  - `department`: string (optional) - Filter by department
  - `start_date`: date (optional) - Report start date
  - `end_date`: date (optional) - Report end date
  - `format`: string (default: "json") - Report format: "json", "csv", "excel"
- **Response Schema** (JSON format):
  - `summary`: dict - Completion summary
  - `by_program`: list[dict] - Completion by program
  - `by_department`: list[dict] - Completion by department
  - `individual_progress`: list[dict] - Individual participant progress
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-management/reports/budget
- **Description**: Generate budget utilization reports
- **Authentication**: Required (Admin/Finance Manager only)
- **Query Parameters**:
  - `year`: int (required) - Report year
  - `department`: string (optional) - Filter by department
  - `format`: string (default: "json") - Report format
- **Response Schema**:
  - `budget_summary`: dict - Overall budget metrics
  - `by_program`: list[dict] - Budget by program
  - `by_month`: list[dict] - Monthly budget utilization
  - `projections`: dict - Budget projections
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/training_programs.py`, `/crud/training_management.py`
- **Models**: `/models/training_program.py`, `/models/program_participant.py`
- **Schemas**: `/schemas/training_management.py`

### Dependencies
- Integrates with Course model for program-course associations
- Uses User model for participant and manager assignments
- Connects with budget management system
- Implements notification system for participant communications
- Uses reporting engine for analytics and metrics
- Integrates with calendar system for scheduling

---

// ...existing content...

## Training Request Statuses

### Overview
Manages the various statuses that training requests can have throughout their lifecycle, from submission to completion.

### Endpoints

#### GET /training-request-statuses/
- **Description**: Retrieve all available training request statuses
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `is_active`: boolean (optional) - Filter by status activity
  - `category`: string (optional) - Filter by status category
- **Response Schema**:
  - `id`: int
  - `name`: string
  - `code`: string
  - `description`: string
  - `category`: string
  - `color`: string
  - `is_active`: boolean
  - `is_final`: boolean
  - `order`: int
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 422: Validation Error

#### POST /training-request-statuses/
- **Description**: Create a new training request status
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `name`: string (required) - Status name
  - `code`: string (required) - Unique status code
  - `description`: string (optional) - Status description
  - `category`: string (required) - Status category: "pending", "approved", "rejected", "completed"
  - `color`: string (optional) - Hex color code for UI display
  - `is_final`: boolean (default: false) - Whether this is a final status
  - `order`: int (required) - Display order
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (duplicate code)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-request-statuses/{status_id}
- **Description**: Retrieve a specific training request status
- **Authentication**: Required
- **Path Parameters**:
  - `status_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `transition_rules`: list[dict] - Valid status transitions
  - `usage_count`: int - Number of requests using this status
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 404: Not Found

#### PUT /training-request-statuses/{status_id}
- **Description**: Update a training request status
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `status_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /training-request-statuses/{status_id}
- **Description**: Delete a training request status (only if not in use)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `status_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (status in use)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /training-request-statuses/{status_id}/transitions
- **Description**: Get valid transitions from a specific status
- **Authentication**: Required
- **Path Parameters**:
  - `status_id`: int (required)
- **Response Schema**:
  - `current_status`: dict - Current status details
  - `valid_transitions`: list[dict] - Valid next statuses
  - `transition_rules`: list[dict] - Transition rules and requirements
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 404: Not Found

#### POST /training-request-statuses/transitions
- **Description**: Create or update status transition rules
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `from_status_id`: int (required) - Source status ID
  - `to_status_id`: int (required) - Target status ID
  - `required_role`: string (optional) - Required role for transition
  - `conditions`: dict (optional) - Additional transition conditions
- **Response Schema**:
  - `id`: int
  - `from_status`: dict - Source status
  - `to_status`: dict - Target status
  - `required_role`: string
  - `conditions`: dict
  - `created_at`: datetime
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (invalid transition)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-request-statuses/workflow
- **Description**: Get the complete status workflow diagram
- **Authentication**: Required
- **Response Schema**:
  - `statuses`: list[dict] - All statuses
  - `transitions`: list[dict] - All valid transitions
  - `workflow_diagram`: dict - Workflow visualization data
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized

#### GET /training-request-statuses/statistics
- **Description**: Get status usage statistics
- **Authentication**: Required (Admin/Manager only)
- **Query Parameters**:
  - `period`: string (default: "month") - Statistics period
  - `department`: string (optional) - Filter by department
- **Response Schema**:
  - `total_requests`: int
  - `by_status`: list[dict] - Requests count by status
  - `average_processing_time`: dict - Processing time by status
  - `bottlenecks`: list[dict] - Identified workflow bottlenecks
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/training_request_statuses.py`
- **Models**: `/models/training_request_status.py`, `/models/status_transition.py`
- **Schemas**: `/schemas/training_request_status.py`

### Dependencies
- Integrates with TrainingRequest model for status tracking
- Uses workflow engine for status transitions
- Implements role-based transition permissions
- Connects with notification system for status changes
- Supports audit logging for status history

---

// ...existing content...

## Training Request Workflow

### Overview
Manages the workflow automation for training requests, including approval processes, notifications, and automated actions based on request status changes.

### Endpoints

#### GET /training-request-workflow/
- **Description**: Retrieve all workflow configurations
- **Authentication**: Required (Admin/Workflow Manager only)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `is_active`: boolean (optional) - Filter by workflow status
  - `department`: string (optional) - Filter by department
- **Response Schema**:
  - `id`: int
  - `name`: string
  - `description`: string
  - `department`: string
  - `trigger_event`: string
  - `conditions`: dict
  - `actions`: list[dict]
  - `is_active`: boolean
  - `priority`: int
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /training-request-workflow/
- **Description**: Create a new workflow rule
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `name`: string (required) - Workflow name
  - `description`: string (optional) - Workflow description
  - `department`: string (optional) - Target department
  - `trigger_event`: string (required) - Trigger event: "status_change", "submission", "approval"
  - `conditions`: dict (required) - Workflow conditions
  - `actions`: list[dict] (required) - Actions to execute
  - `priority`: int (default: 100) - Execution priority
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-request-workflow/{workflow_id}
- **Description**: Retrieve a specific workflow configuration
- **Authentication**: Required (Admin/Workflow Manager only)
- **Path Parameters**:
  - `workflow_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `execution_history`: list[dict] - Recent workflow executions
  - `statistics`: dict - Workflow performance metrics
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /training-request-workflow/{workflow_id}
- **Description**: Update a workflow configuration
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `workflow_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /training-request-workflow/{workflow_id}
- **Description**: Delete a workflow configuration
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `workflow_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /training-request-workflow/{workflow_id}/execute
- **Description**: Manually trigger a workflow execution
- **Authentication**: Required (Admin/Workflow Manager only)
- **Path Parameters**:
  - `workflow_id`: int (required)
- **Request Schema**:
  - `request_id`: int (required) - Training request ID
  - `force`: boolean (default: false) - Force execution even if conditions not met
- **Response Schema**:
  - `execution_id`: string
  - `status`: string
  - `actions_executed`: list[dict]
  - `errors`: list[dict]
  - `executed_at`: datetime
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### GET /training-request-workflow/executions/
- **Description**: Get workflow execution history
- **Authentication**: Required (Admin/Workflow Manager only)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `workflow_id`: int (optional) - Filter by workflow
  - `status`: string (optional) - Filter by execution status
  - `start_date`: datetime (optional) - Filter from date
  - `end_date`: datetime (optional) - Filter to date
- **Response Schema**:
  - `id`: string
  - `workflow_id`: int
  - `workflow_name`: string
  - `request_id`: int
  - `trigger_event`: string
  - `status`: string
  - `actions_executed`: list[dict]
  - `execution_time`: float
  - `errors`: list[dict]
  - `executed_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-request-workflow/templates/
- **Description**: Get available workflow templates
- **Authentication**: Required (Admin only)
- **Response Schema**:
  - `templates`: list[dict] - Available workflow templates
    - `id`: string
    - `name`: string
    - `description`: string
    - `category`: string
    - `template`: dict - Template configuration
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden

#### POST /training-request-workflow/from-template
- **Description**: Create workflow from template
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `template_id`: string (required) - Template ID
  - `name`: string (required) - Workflow name
  - `department`: string (optional) - Target department
  - `customizations`: dict (optional) - Template customizations
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /training-request-workflow/test
- **Description**: Test workflow configuration without executing
- **Authentication**: Required (Admin only)
- **Request Schema**:
  - `workflow_config`: dict (required) - Workflow configuration to test
  - `test_data`: dict (required) - Test request data
- **Response Schema**:
  - `valid`: boolean
  - `conditions_met`: boolean
  - `actions_to_execute`: list[dict]
  - `validation_errors`: list[dict]
  - `warnings`: list[dict]
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /training-request-workflow/statistics
- **Description**: Get workflow performance statistics
- **Authentication**: Required (Admin/Workflow Manager only)
- **Query Parameters**:
  - `period`: string (default: "month") - Statistics period
  - `workflow_id`: int (optional) - Filter by workflow
- **Response Schema**:
  - `total_executions`: int
  - `success_rate`: float
  - `average_execution_time`: float
  - `most_active_workflows`: list[dict]
  - `error_summary`: list[dict]
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/workflow.py`, `/crud/workflow_executions.py`
- **Models**: `/models/workflow.py`, `/models/workflow_execution.py`
- **Schemas**: `/schemas/workflow.py`

### Dependencies
- Integrates with TrainingRequest model for trigger events
- Uses TrainingRequestStatus model for status-based workflows
- Implements notification system for automated communications
- Connects with approval system for automated approvals
- Uses background task queue for asynchronous execution
- Integrates with audit logging for workflow tracking

---

// ...existing content...

## Training Requests

### Overview
Manages training requests submitted by users, including request lifecycle, approvals, and fulfillment tracking.

### Endpoints

#### GET /training-requests/
- **Description**: Retrieve training requests with filtering and search options
- **Authentication**: Required
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `status`: string (optional) - Filter by request status
  - `requester_id`: int (optional) - Filter by requester (Admin only)
  - `department`: string (optional) - Filter by department
  - `priority`: string (optional) - Filter by priority level
  - `start_date`: date (optional) - Filter requests from date
  - `end_date`: date (optional) - Filter requests to date
  - `search`: string (optional) - Search in title and description
- **Response Schema**:
  - `id`: int
  - `title`: string
  - `description`: string
  - `requester_id`: int
  - `requester_name`: string
  - `department`: string
  - `priority`: string
  - `status`: string
  - `status_color`: string
  - `request_type`: string
  - `justification`: string
  - `expected_start_date`: date
  - `expected_completion_date`: date
  - `budget_estimate`: float
  - `participants_count`: int
  - `approved_by`: int (nullable)
  - `approved_at`: datetime (nullable)
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 422: Validation Error

#### POST /training-requests/
- **Description**: Submit a new training request
- **Authentication**: Required
- **Request Schema**:
  - `title`: string (required) - Request title
  - `description`: string (required) - Detailed description
  - `department`: string (required) - Requesting department
  - `priority`: string (required) - "low", "medium", "high", "urgent"
  - `request_type`: string (required) - "internal", "external", "online", "certification"
  - `justification`: string (required) - Business justification
  - `expected_start_date`: date (required) - Preferred start date
  - `expected_completion_date`: date (optional) - Expected completion
  - `budget_estimate`: float (optional) - Estimated budget
  - `participants_count`: int (required) - Number of participants
  - `skills_objectives`: list[string] (optional) - Learning objectives
  - `preferred_provider`: string (optional) - Preferred training provider
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request
  - 401: Unauthorized
  - 422: Validation Error

#### GET /training-requests/{request_id}
- **Description**: Retrieve detailed information about a training request
- **Authentication**: Required
- **Path Parameters**:
  - `request_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `skills_objectives`: list[string] - Learning objectives
  - `preferred_provider`: string - Preferred provider
  - `status_history`: list[dict] - Status change history
  - `comments`: list[dict] - Request comments
  - `attachments`: list[dict] - Attached documents
  - `assigned_courses`: list[dict] - Courses assigned to fulfill request
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /training-requests/{request_id}
- **Description**: Update a training request (only if in draft or pending status)
- **Authentication**: Required (Requester/Admin only)
- **Path Parameters**:
  - `request_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (cannot edit in current status)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /training-requests/{request_id}
- **Description**: Delete a training request (only if in draft status)
- **Authentication**: Required (Requester/Admin only)
- **Path Parameters**:
  - `request_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (cannot delete in current status)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /training-requests/{request_id}/submit
- **Description**: Submit a draft request for approval
- **Authentication**: Required (Requester only)
- **Path Parameters**:
  - `request_id`: int (required)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (not in draft status, missing required fields)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /training-requests/{request_id}/approve
- **Description**: Approve a training request
- **Authentication**: Required (Manager/Admin only)
- **Path Parameters**:
  - `request_id`: int (required)
- **Request Schema**:
  - `comments`: string (optional) - Approval comments
  - `budget_approved`: float (optional) - Approved budget amount
  - `conditions`: string (optional) - Approval conditions
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (cannot approve in current status)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### POST /training-requests/{request_id}/reject
- **Description**: Reject a training request
- **Authentication**: Required (Manager/Admin only)
- **Path Parameters**:
  - `request_id`: int (required)
- **Request Schema**:
  - `reason`: string (required) - Rejection reason
  - `comments`: string (optional) - Additional comments
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (cannot reject in current status)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### POST /training-requests/{request_id}/assign-course
- **Description**: Assign a course to fulfill a training request
- **Authentication**: Required (Training Manager/Admin only)
- **Path Parameters**:
  - `request_id`: int (required)
- **Request Schema**:
  - `course_id`: int (required) - Course to assign
  - `notes`: string (optional) - Assignment notes
- **Response Schema**:
  - `request_id`: int
  - `course_id`: int
  - `assigned_at`: datetime
  - `notes`: string
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (course full, incompatible dates)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### GET /training-requests/{request_id}/comments
- **Description**: Get all comments for a training request
- **Authentication**: Required
- **Path Parameters**:
  - `request_id`: int (required)
- **Response Schema**:
  - `comments`: list[dict] - Request comments
    - `id`: int
    - `content`: string
    - `author_id`: int
    - `author_name`: string
    - `is_internal`: boolean
    - `created_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /training-requests/{request_id}/comments
- **Description**: Add a comment to a training request
- **Authentication**: Required
- **Path Parameters**:
  - `request_id`: int (required)
- **Request Schema**:
  - `content`: string (required) - Comment content
  - `is_internal`: boolean (default: false) - Internal comment flag
- **Response Schema**:
  - `id`: int
  - `content`: string
  - `author_id`: int
  - `author_name`: string
  - `is_internal`: boolean
  - `created_at`: datetime
- **Status Codes**:
  - 201: Created
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### GET /training-requests/dashboard
- **Description**: Get training requests dashboard for current user
- **Authentication**: Required
- **Response Schema**:
  - `my_requests`: dict - User's request statistics
  - `pending_approvals`: int - Requests awaiting user's approval
  - `recent_activity`: list[dict] - Recent request activity
  - `upcoming_deadlines`: list[dict] - Upcoming request deadlines
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized

#### GET /training-requests/export
- **Description**: Export training requests to CSV/Excel
- **Authentication**: Required (Manager/Admin only)
- **Query Parameters**: Same as GET /training-requests/ plus:
  - `format`: string (default: "csv") - Export format: "csv" or "excel"
  - `fields`: list[string] (optional) - Specific fields to export
- **Response**: File download
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/training_requests.py`, `/crud/request_comments.py`
- **Models**: `/models/training_request.py`, `/models/request_comment.py`
- **Schemas**: `/schemas/training_request.py`

### Dependencies
- Integrates with TrainingRequestStatus for status management
- Uses TrainingRequestWorkflow for automated processing
- Connects with Course model for request fulfillment
- Implements approval workflow with role-based permissions
- Uses notification system for status updates
- Integrates with document management for attachments

---

// ...existing content...

## Users

### Overview
Manages user accounts, profiles, authentication data, and user-related operations within the SIGECAP system.

### Endpoints

#### GET /users/
- **Description**: Retrieve users with filtering and search options
- **Authentication**: Required (Admin/HR only for full list, users can only see limited info)
- **Query Parameters**:
  - `skip`: int (default: 0) - Number of records to skip
  - `limit`: int (default: 100) - Maximum number of records to return
  - `search`: string (optional) - Search by name, username, or email
  - `department`: string (optional) - Filter by department
  - `role`: string (optional) - Filter by role
  - `is_active`: boolean (optional) - Filter by account status
  - `is_verified`: boolean (optional) - Filter by verification status
- **Response Schema**:
  - `id`: int
  - `username`: string
  - `email`: string
  - `full_name`: string
  - `first_name`: string
  - `last_name`: string
  - `department`: string
  - `position`: string
  - `phone`: string
  - `is_active`: boolean
  - `is_verified`: boolean
  - `last_login`: datetime (nullable)
  - `created_at`: datetime
  - `updated_at`: datetime
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### POST /users/
- **Description**: Create a new user account
- **Authentication**: Required (Admin/HR only)
- **Request Schema**:
  - `username`: string (required) - Unique username
  - `email`: string (required) - User email address
  - `password`: string (required) - User password
  - `first_name`: string (required) - First name
  - `last_name`: string (required) - Last name
  - `department`: string (required) - User department
  - `position`: string (optional) - Job position
  - `phone`: string (optional) - Phone number
  - `role_ids`: list[int] (optional) - Initial role assignments
  - `send_welcome_email`: boolean (default: true) - Send welcome email
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 201: Created
  - 400: Bad Request (duplicate username/email)
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

#### GET /users/{user_id}
- **Description**: Retrieve detailed user information
- **Authentication**: Required (Admin/HR for all users, users for self)
- **Path Parameters**:
  - `user_id`: int (required)
- **Response Schema**: Same as GET response plus:
  - `roles`: list[dict] - User roles
  - `permissions`: list[dict] - User permissions
  - `training_history`: list[dict] - Completed trainings (if accessible)
  - `profile_picture`: string (nullable) - Profile picture URL
  - `bio`: string (nullable) - User biography
  - `preferences`: dict - User preferences
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### PUT /users/{user_id}
- **Description**: Update user information
- **Authentication**: Required (Admin/HR for all users, users for self with restrictions)
- **Path Parameters**:
  - `user_id`: int (required)
- **Request Schema**: Same as POST request (all fields optional, excluding username)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### DELETE /users/{user_id}
- **Description**: Deactivate user account (soft delete)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `user_id`: int (required)
- **Status Codes**:
  - 204: No Content
  - 400: Bad Request (cannot delete own account)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /users/me
- **Description**: Get current user's profile information
- **Authentication**: Required
- **Response Schema**: Same as GET /users/{user_id} response
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized

#### PUT /users/me
- **Description**: Update current user's profile
- **Authentication**: Required
- **Request Schema**:
  - `first_name`: string (optional)
  - `last_name`: string (optional)
  - `phone`: string (optional)
  - `bio`: string (optional)
  - `preferences`: dict (optional)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request
  - 401: Unauthorized
  - 422: Validation Error

#### POST /users/{user_id}/activate
- **Description**: Activate a deactivated user account
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `user_id`: int (required)
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (already active)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /users/{user_id}/deactivate
- **Description**: Deactivate a user account
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `user_id`: int (required)
- **Request Schema**:
  - `reason`: string (optional) - Deactivation reason
- **Response Schema**: Same as GET response
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (cannot deactivate own account)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /users/{user_id}/reset-password
- **Description**: Reset user password (admin action)
- **Authentication**: Required (Admin only)
- **Path Parameters**:
  - `user_id`: int (required)
- **Request Schema**:
  - `new_password`: string (optional) - New password (if not provided, generates random)
  - `force_change`: boolean (default: true) - Force password change on next login
  - `send_email`: boolean (default: true) - Send new password via email
- **Response Schema**:
  - `message`: string - Success message
  - `temporary_password`: string (only if generated)
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 422: Validation Error

#### POST /users/{user_id}/send-verification
- **Description**: Send email verification to user
- **Authentication**: Required (Admin or self)
- **Path Parameters**:
  - `user_id`: int (required)
- **Response Schema**:
  - `message`: string - Confirmation message
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (already verified)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### POST /users/verify-email
- **Description**: Verify user email with verification token
- **Authentication**: Not required
- **Request Schema**:
  - `token`: string (required) - Email verification token
- **Response Schema**:
  - `message`: string - Success message
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (invalid/expired token)
  - 422: Validation Error

#### POST /users/{user_id}/upload-avatar
- **Description**: Upload user profile picture
- **Authentication**: Required (Admin or self)
- **Path Parameters**:
  - `user_id`: int (required)
- **Request Schema** (multipart/form-data):
  - `avatar`: file (required) - Image file
- **Response Schema**:
  - `profile_picture`: string - New profile picture URL
- **Status Codes**:
  - 200: Success
  - 400: Bad Request (invalid image format)
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found
  - 413: Payload Too Large

#### GET /users/{user_id}/training-history
- **Description**: Get user's training history
- **Authentication**: Required (Admin/HR for all users, users for self)
- **Path Parameters**:
  - `user_id`: int (required)
- **Query Parameters**:
  - `status`: string (optional) - Filter by completion status
  - `year`: int (optional) - Filter by year
- **Response Schema**:
  - `trainings`: list[dict] - Training records
  - `statistics`: dict - Training statistics
  - `certificates`: list[dict] - Earned certificates
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 404: Not Found

#### GET /users/search
- **Description**: Advanced user search with multiple criteria
- **Authentication**: Required
- **Query Parameters**:
  - `q`: string (required) - Search query
  - `filters`: dict (optional) - Advanced filters
  - `sort`: string (optional) - Sort criteria
- **Response Schema**: Same as GET /users/ response
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 422: Validation Error

#### GET /users/export
- **Description**: Export users data to CSV/Excel
- **Authentication**: Required (Admin/HR only)
- **Query Parameters**: Same as GET /users/ plus:
  - `format`: string (default: "csv") - Export format
  - `fields`: list[string] (optional) - Specific fields to export
- **Response**: File download
- **Status Codes**:
  - 200: Success
  - 401: Unauthorized
  - 403: Forbidden
  - 422: Validation Error

### Related Files
- **CRUD**: `/crud/users.py`, `/crud/user_roles.py`
- **Models**: `/models/user.py`, `/models/user_profile.py`
- **Schemas**: `/schemas/user.py`, `/schemas/user_profile.py`

### Dependencies
- Integrates with Role model for role assignments
- Uses Permission model for access control
- Connects with authentication system for login management
- Implements email service for notifications and verification
- Uses file storage for profile pictures
- Integrates with training system for history tracking
- Supports audit logging for user actions

---

## Summary

This documentation covers all 15 API endpoint modules in the SIGECAP backend system:

1. **Attendances** - Manage training session attendance
2. **Course Participants** - Handle course enrollment and participation
3. **Courses** - Manage training courses and scheduling
4. **CSRF** - Provide security token management
5. **Documents** - Handle file uploads and document management
6. **Evaluations** - Manage course evaluations and feedback
7. **Google Sheets** - Integrate with Google Sheets for data operations
8. **Login** - Handle authentication and session management
9. **Permissions** - Manage system permissions and access control
10. **Roles** - Handle user roles and role-based permissions
11. **Training Management** - Comprehensive training program administration
12. **Training Request Statuses** - Manage request status lifecycle
13. **Training Request Workflow** - Automate training request processing
14. **Training Requests** - Handle training request submissions and approvals
15. **Users** - Manage user accounts and profiles

Each endpoint is documented with complete HTTP methods, authentication requirements, request/response schemas, status codes, and their relationships with CRUD operations, models, and schemas within the system architecture.