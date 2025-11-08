# Teacher Content Management Platform - API Documentation

**Version:** 1.0.0
**Last Updated:** January 2025
**Base URL:** `http://localhost:8000/v1`

---

## Table of Contents

1. [Introduction](#introduction)
2. [Technology Stack](#technology-stack)
3. [Authentication & Security](#authentication--security)
4. [Quick Start Guide](#quick-start-guide)
5. [Setup & Administration APIs](#setup--administration-apis)
6. [Content Import & Processing APIs](#content-import--processing-apis)
7. [AI Content Generation APIs](#ai-content-generation-apis)
8. [Student Progress & Logging](#student-progress--logging)
9. [Intelligence & Analytics APIs](#intelligence--analytics-apis)
10. [Export & Integration APIs](#export--integration-apis)
11. [Technical Reference](#technical-reference)
12. [Integration Guide](#integration-guide)
13. [Error Handling](#error-handling)
14. [Rate Limiting](#rate-limiting)

---

## Introduction

The Teacher Content Management Platform is a comprehensive API for managing educational content, generating AI-powered teaching materials, and analyzing curriculum data. The platform enables teachers and educational institutions to:

- **Manage Curriculum:** Create and organize courses, lessons, and educational content
- **Import Content:** Upload documents, videos, audio, and images with automatic processing
- **Generate Materials:** Use AI to create summaries, quizzes, flashcards, lesson plans, slides, and more
- **Track Progress:** Monitor content creation and student engagement (integration ready)
- **Analyze Data:** Search, filter, and export educational data for insights
- **Export Content:** Generate PDF, DOCX, and JSON exports of all materials

---

## Technology Stack

### Backend
- **Framework:** FastAPI 0.104.1 (Python 3.12+)
- **Database:** SQLite with SQLAlchemy 2.0 (async)
- **Authentication:** JWT with refresh tokens (bcrypt password hashing)
- **AI Services:** OpenAI GPT-4 for content generation
- **Media Processing:**
  - OpenAI Whisper / Deepgram for audio/video transcription
  - PaddleOCR for image text extraction
  - python-docx, PyPDF2, python-pptx for document processing

### Frontend
- **Framework:** Vue 3 with Composition API
- **State Management:** Pinia
- **Routing:** Vue Router 4
- **HTTP Client:** Axios with automatic token refresh
- **UI Components:** Lucide icons, SortableJS for drag-and-drop

---

## Authentication & Security

### Authentication Flow

The platform uses JWT (JSON Web Tokens) with a refresh token mechanism for secure authentication.

```
1. User Registration/Login → Receives access token + refresh token
2. Access token expires in 60 minutes
3. Refresh token expires in 30 days
4. Use refresh token to get new access token when expired
5. All API requests require: Authorization: Bearer <access_token>
```

### Security Features

- **Password Hashing:** bcrypt with salt
- **Token Expiration:** Short-lived access tokens (60 min), long-lived refresh tokens (30 days)
- **Token Storage:** Refresh tokens stored in database with user association
- **Email Verification:** 6-digit verification codes (currently mocked for development)
- **Rate Limiting:** 100 requests per hour for AI generation endpoints

---

## Quick Start Guide

### 1. Register a New User

```http
POST /v1/auth/register
Content-Type: application/json

{
  "email": "teacher@school.edu",
  "password": "SecurePass123!",
  "first_name": "Jane",
  "last_name": "Smith",
  "role": "teacher"
}
```

**Response (201):**
```json
{
  "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "refresh_token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
  "expires_in": 3600,
  "user": {
    "id": "user_94dce572a034",
    "email": "teacher@school.edu",
    "first_name": "Jane",
    "last_name": "Smith",
    "role": "teacher",
    "email_verified": false,
    "created_at": "2025-01-15T10:30:00Z"
  }
}
```

### 2. Create a Course

```http
POST /v1/courses
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Introduction to Python Programming",
  "description": "A comprehensive course for beginners to learn Python",
  "subject": "Computer Science",
  "grade_level": "9-12",
  "tags": ["programming", "python", "beginner"]
}
```

**Response (201):**
```json
{
  "id": "course_a1b2c3d4e5f6",
  "title": "Introduction to Python Programming",
  "description": "A comprehensive course for beginners to learn Python",
  "subject": "Computer Science",
  "grade_level": "9-12",
  "tags": ["programming", "python", "beginner"],
  "created_by": "user_94dce572a034",
  "created_at": "2025-01-15T11:00:00Z",
  "updated_at": "2025-01-15T11:00:00Z",
  "lesson_count": 0
}
```

### 3. Add a Lesson

```http
POST /v1/courses/course_a1b2c3d4e5f6/lessons
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Variables and Data Types",
  "description": "Learn about Python variables, strings, numbers, and booleans",
  "order": 1,
  "topics": ["variables", "data types", "strings", "numbers"]
}
```

**Response (201):**
```json
{
  "id": "lesson_f6e5d4c3b2a1",
  "course_id": "course_a1b2c3d4e5f6",
  "title": "Variables and Data Types",
  "description": "Learn about Python variables, strings, numbers, and booleans",
  "order": 1,
  "topics": ["variables", "data types", "strings", "numbers"],
  "content_count": 0,
  "created_at": "2025-01-15T11:15:00Z",
  "updated_at": "2025-01-15T11:15:00Z"
}
```

### 4. Import Content

```http
POST /v1/import/upload
Authorization: Bearer <access_token>
Content-Type: multipart/form-data

lesson_id=lesson_f6e5d4c3b2a1
file=@lecture_video.mp4
```

**Response (201):**
```json
{
  "id": "import_1a2b3c4d5e6f",
  "lesson_id": "lesson_f6e5d4c3b2a1",
  "file_name": "lecture_video.mp4",
  "file_size": 52428800,
  "file_type": "mp4",
  "media_type": "video",
  "status": "completed",
  "extracted_text": "In this lecture, we'll explore Python variables...",
  "topics": ["variables", "data types", "assignment"],
  "key_points": [
    "Variables store data values",
    "Python is dynamically typed",
    "Common types: int, float, str, bool"
  ],
  "duration": 1200,
  "language_detected": "en",
  "ai_summary": "This lecture introduces fundamental concepts of Python variables...",
  "main_concepts": ["Variables", "Data Types", "Type Conversion"],
  "created_at": "2025-01-15T11:30:00Z",
  "completed_at": "2025-01-15T11:35:00Z"
}
```

### 5. Generate AI Content

```http
POST /v1/generate/quiz
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_f6e5d4c3b2a1",
  "import_id": "import_1a2b3c4d5e6f",
  "num_questions": 10,
  "difficulty": "medium"
}
```

**Response (200):**
```json
{
  "id": "content_9z8y7x6w5v4u",
  "lesson_id": "lesson_f6e5d4c3b2a1",
  "content_type": "quiz",
  "title": "Python Variables Quiz",
  "content": {
    "title": "Python Variables Quiz",
    "instructions": "Answer all questions to test your understanding",
    "questions": [
      {
        "question": "What is a variable in Python?",
        "type": "multiple_choice",
        "options": [
          "A container for storing data values",
          "A function that performs calculations",
          "A type of loop",
          "A conditional statement"
        ],
        "correct_answer": 0,
        "explanation": "Variables are containers that store data values in memory"
      }
    ],
    "answer_key": [0, 2, 1, 3, 0, 1, 2, 0, 3, 1]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T12:00:00Z",
  "updated_at": "2025-01-15T12:00:00Z"
}
```

---

## Setup & Administration APIs

### User Management

#### Get Current User Profile

```http
GET /v1/users/me
Authorization: Bearer <access_token>
```

**Response (200):**
```json
{
  "id": "user_94dce572a034",
  "email": "teacher@school.edu",
  "first_name": "Jane",
  "last_name": "Smith",
  "role": "teacher",
  "email_verified": true,
  "preferences": {
    "theme": "light",
    "notifications": true
  },
  "created_at": "2025-01-15T10:30:00Z",
  "updated_at": "2025-01-15T10:30:00Z"
}
```

#### Update User Profile

```http
PATCH /v1/users/me
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "first_name": "Jane",
  "last_name": "Doe",
  "preferences": {
    "theme": "dark",
    "notifications": false
  }
}
```

**Response (200):** Returns updated user object

#### Change Password

```http
POST /v1/users/me/password
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "current_password": "OldPass123!",
  "new_password": "NewSecurePass456!"
}
```

**Response (200):**
```json
{
  "message": "Password updated successfully"
}
```

---

### Course Management

#### Create a Course

```http
POST /v1/courses
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Advanced Mathematics",
  "description": "Calculus and advanced algebra concepts",
  "subject": "Mathematics",
  "grade_level": "11-12",
  "tags": ["math", "calculus", "algebra", "advanced"]
}
```

**Response (201):**
```json
{
  "id": "course_b2c3d4e5f6g7",
  "title": "Advanced Mathematics",
  "description": "Calculus and advanced algebra concepts",
  "subject": "Mathematics",
  "grade_level": "11-12",
  "tags": ["math", "calculus", "algebra", "advanced"],
  "created_by": "user_94dce572a034",
  "created_at": "2025-01-15T13:00:00Z",
  "updated_at": "2025-01-15T13:00:00Z",
  "lesson_count": 0
}
```

#### List All Courses

```http
GET /v1/courses?page=1&limit=20&subject=Mathematics&grade_level=11-12&search=calculus
Authorization: Bearer <access_token>
```

**Query Parameters:**
- `page` (integer, default: 1) - Page number
- `limit` (integer, default: 20, max: 100) - Items per page
- `subject` (string, optional) - Filter by subject
- `grade_level` (string, optional) - Filter by grade level
- `search` (string, optional) - Search in title and description

**Response (200):**
```json
{
  "data": [
    {
      "id": "course_b2c3d4e5f6g7",
      "title": "Advanced Mathematics",
      "description": "Calculus and advanced algebra concepts",
      "subject": "Mathematics",
      "grade_level": "11-12",
      "tags": ["math", "calculus", "algebra", "advanced"],
      "created_by": "user_94dce572a034",
      "created_at": "2025-01-15T13:00:00Z",
      "updated_at": "2025-01-15T13:00:00Z",
      "lesson_count": 12
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 45,
    "total_pages": 3,
    "has_next": true,
    "has_prev": false
  }
}
```

#### Get Single Course with Lessons

```http
GET /v1/courses/course_b2c3d4e5f6g7
Authorization: Bearer <access_token>
```

**Response (200):**
```json
{
  "id": "course_b2c3d4e5f6g7",
  "title": "Advanced Mathematics",
  "description": "Calculus and advanced algebra concepts",
  "subject": "Mathematics",
  "grade_level": "11-12",
  "tags": ["math", "calculus", "algebra", "advanced"],
  "created_by": "user_94dce572a034",
  "created_at": "2025-01-15T13:00:00Z",
  "updated_at": "2025-01-15T13:00:00Z",
  "lessons": [
    {
      "id": "lesson_c3d4e5f6g7h8",
      "course_id": "course_b2c3d4e5f6g7",
      "title": "Introduction to Derivatives",
      "description": "Understanding the concept of derivatives",
      "order": 1,
      "topics": ["derivatives", "limits", "rate of change"],
      "content_count": 5,
      "created_at": "2025-01-15T13:15:00Z",
      "updated_at": "2025-01-15T13:15:00Z"
    }
  ]
}
```

#### Update Course

```http
PATCH /v1/courses/course_b2c3d4e5f6g7
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Advanced Calculus",
  "description": "Updated description",
  "tags": ["math", "calculus", "advanced", "derivatives"]
}
```

**Response (200):** Returns updated course object

#### Delete Course (Soft Delete)

```http
DELETE /v1/courses/course_b2c3d4e5f6g7
Authorization: Bearer <access_token>
```

**Response (204):** No content

---

### Lesson Management

#### Create a Lesson

```http
POST /v1/courses/course_b2c3d4e5f6g7/lessons
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Integration Techniques",
  "description": "Learn various methods of integration",
  "order": 5,
  "topics": ["integration", "u-substitution", "integration by parts"]
}
```

**Response (201):**
```json
{
  "id": "lesson_d4e5f6g7h8i9",
  "course_id": "course_b2c3d4e5f6g7",
  "title": "Integration Techniques",
  "description": "Learn various methods of integration",
  "order": 5,
  "topics": ["integration", "u-substitution", "integration by parts"],
  "content_count": 0,
  "created_at": "2025-01-15T14:00:00Z",
  "updated_at": "2025-01-15T14:00:00Z"
}
```

#### List Lessons for a Course

```http
GET /v1/courses/course_b2c3d4e5f6g7/lessons
Authorization: Bearer <access_token>
```

**Response (200):** Returns array of lesson objects ordered by `order` field

#### Get Single Lesson

```http
GET /v1/courses/lessons/lesson_d4e5f6g7h8i9
Authorization: Bearer <access_token>
```

**Response (200):** Returns lesson object

#### Update Lesson

```http
PATCH /v1/courses/lessons/lesson_d4e5f6g7h8i9
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Advanced Integration Techniques",
  "topics": ["integration", "u-substitution", "integration by parts", "partial fractions"]
}
```

**Response (200):** Returns updated lesson object

#### Delete Lesson

```http
DELETE /v1/courses/lessons/lesson_d4e5f6g7h8i9
Authorization: Bearer <access_token>
```

**Response (204):** No content

#### Reorder Lessons (Drag & Drop)

```http
PUT /v1/courses/course_b2c3d4e5f6g7/lessons/reorder
Authorization: Bearer <access_token>
Content-Type: application/json

[
  {"id": "lesson_c3d4e5f6g7h8", "order": 1},
  {"id": "lesson_d4e5f6g7h8i9", "order": 2},
  {"id": "lesson_e5f6g7h8i9j0", "order": 3}
]
```

**Response (200):**
```json
{
  "message": "Lessons reordered successfully"
}
```

---

## Content Import & Processing APIs

The platform supports automatic processing of various file types with AI-powered content extraction, transcription, and analysis.

### Supported File Types

| Category | Formats | Processing |
|----------|---------|------------|
| **Documents** | PDF, DOCX, DOC | Text extraction |
| **Presentations** | PPTX, PPT | Text and structure extraction |
| **Images** | PNG, JPG, JPEG, GIF, BMP, TIFF | OCR (PaddleOCR) |
| **Markdown** | MD, MARKDOWN | Direct parsing |
| **Video** | MP4, MOV, AVI, MKV, WEBM, M4V | Audio extraction + transcription |
| **Audio** | MP3, WAV, M4A, AAC, OGG, FLAC, WMA | Transcription (OpenAI/Deepgram) |

### Upload and Process File

```http
POST /v1/import/upload
Authorization: Bearer <access_token>
Content-Type: multipart/form-data

lesson_id: lesson_d4e5f6g7h8i9
file: @calculus_lecture.mp4
```

**Processing Pipeline:**
1. File validation and storage
2. Content extraction (text/audio/video)
3. AI transcription (for audio/video)
4. AI analysis and summarization
5. Topic extraction and key points identification

**Response (201):**
```json
{
  "id": "import_2b3c4d5e6f7g",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "file_name": "calculus_lecture.mp4",
  "file_size": 104857600,
  "file_type": "mp4",
  "media_type": "video",
  "status": "completed",
  "extracted_text": "Today we'll explore integration techniques starting with u-substitution...",
  "topics": ["integration", "u-substitution", "calculus"],
  "key_points": [
    "U-substitution simplifies complex integrals",
    "Choose u to make the derivative appear in the integral",
    "Don't forget to change the bounds for definite integrals"
  ],
  "duration": 1800,
  "language_detected": "en",
  "transcription_service": "openai",
  "transcription_confidence": 96.5,
  "audio_url": "/uploads/audio/calculus_lecture_audio.mp3",
  "ai_summary": "This lecture covers u-substitution as a fundamental integration technique...",
  "main_concepts": ["U-Substitution", "Integration", "Variable Transformation"],
  "content_structure": {
    "sections": [
      "Introduction to Integration",
      "U-Substitution Method",
      "Practice Problems",
      "Common Mistakes"
    ]
  },
  "educational_metadata": {
    "difficulty": "intermediate",
    "estimated_duration": "30 minutes",
    "prerequisites": ["derivatives", "basic integration"]
  },
  "processing_tokens_used": 2400,
  "error_message": null,
  "created_at": "2025-01-15T14:30:00Z",
  "completed_at": "2025-01-15T14:35:00Z"
}
```

**Status Values:**
- `pending` - File uploaded, waiting to process
- `processing` - Currently being processed
- `completed` - Successfully processed
- `failed` - Processing failed (see error_message)

### List Imports for a Lesson

```http
GET /v1/import/lesson/lesson_d4e5f6g7h8i9/imports
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  {
    "id": "import_2b3c4d5e6f7g",
    "lesson_id": "lesson_d4e5f6g7h8i9",
    "file_name": "calculus_lecture.mp4",
    "file_size": 104857600,
    "file_type": "mp4",
    "media_type": "video",
    "status": "completed",
    "created_at": "2025-01-15T14:30:00Z",
    "completed_at": "2025-01-15T14:35:00Z"
  }
]
```

### Get Import Details

```http
GET /v1/import/import/import_2b3c4d5e6f7g
Authorization: Bearer <access_token>
```

**Response (200):** Returns complete import object with all extracted data

### Delete Import

```http
DELETE /v1/import/import/import_2b3c4d5e6f7g
Authorization: Bearer <access_token>
```

**Response (204):** No content

---

## AI Content Generation APIs

Generate educational materials using AI based on imported content. All endpoints support three modes:

1. **Automatic Mode** (default): Uses most recent import for the lesson
2. **Context-Aware Mode**: Specify `import_id` to use AI-processed summaries (recommended, uses fewer tokens)
3. **Legacy Mode**: Provide `source_text` directly (uses more tokens)

### Common Parameters

All generation endpoints accept these common parameters:

- `lesson_id` (required) - The lesson to generate content for
- `import_id` (optional) - Use specific import's AI summary as context
- `source_text` (optional) - Provide text directly (not recommended)
- `custom_instructions` (optional) - Additional instructions for AI

### Content Types

1. Summary
2. Quiz
3. Flashcards
4. Lesson Plan
5. Slides (LaTeX Beamer format)
6. Learning Objectives
7. Vocabulary List
8. Worksheet
9. Classroom Activities
10. Exit Ticket

---

### 1. Generate Summary

```http
POST /v1/generate/summary
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "length": "medium",
  "focus_areas": ["key concepts", "formulas", "examples"],
  "custom_instructions": "Focus on practical applications"
}
```

**Parameters:**
- `length` (optional): "short" (150 words), "medium" (300 words), "long" (500 words)
- `focus_areas` (optional): Array of areas to emphasize

**Response (200):**
```json
{
  "id": "content_3c4d5e6f7g8h",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "summary",
  "title": "Integration Techniques - Summary",
  "content": {
    "title": "Integration Techniques - Summary",
    "summary": "U-substitution is a fundamental integration technique that simplifies complex integrals...",
    "key_takeaways": [
      "U-substitution transforms difficult integrals into simpler forms",
      "Choose u such that its derivative appears in the integrand",
      "For definite integrals, update the integration bounds"
    ],
    "word_count": 285
  },
  "source_text": "Today we'll explore integration techniques...",
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T15:00:00Z",
  "updated_at": "2025-01-15T15:00:00Z"
}
```

---

### 2. Generate Quiz

```http
POST /v1/generate/quiz
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_questions": 10,
  "question_types": ["multiple_choice", "true_false"],
  "difficulty": "medium",
  "custom_instructions": "Include formula-based questions"
}
```

**Parameters:**
- `num_questions` (optional, default: 10) - Number of questions to generate
- `question_types` (optional): ["multiple_choice", "true_false", "short_answer"]
- `difficulty` (optional): "easy", "medium", "hard"

**Response (200):**
```json
{
  "id": "content_4d5e6f7g8h9i",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "quiz",
  "title": "Integration Techniques Quiz",
  "content": {
    "title": "Integration Techniques Quiz",
    "instructions": "Choose the best answer for each question",
    "questions": [
      {
        "question": "In u-substitution, what should you choose for u?",
        "type": "multiple_choice",
        "options": [
          "A function whose derivative appears in the integrand",
          "The most complex term",
          "Always the denominator",
          "The constant term"
        ],
        "correct_answer": 0,
        "explanation": "Choosing u so that du appears in the integrand simplifies the integral"
      },
      {
        "question": "U-substitution can be applied to all integrals",
        "type": "true_false",
        "options": ["True", "False"],
        "correct_answer": 1,
        "explanation": "U-substitution works for many but not all integrals. Some require other techniques."
      }
    ],
    "answer_key": [0, 1, 2, 0, 3, 1, 2, 0, 1, 3]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T15:10:00Z",
  "updated_at": "2025-01-15T15:10:00Z"
}
```

---

### 3. Generate Flashcards

```http
POST /v1/generate/flashcards
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_cards": 20,
  "focus_topics": ["u-substitution", "integration formulas"],
  "custom_instructions": "Include formula examples on cards"
}
```

**Parameters:**
- `num_cards` (optional, default: 20) - Number of flashcards
- `focus_topics` (optional) - Specific topics to focus on

**Response (200):**
```json
{
  "id": "content_5e6f7g8h9i0j",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "flashcards",
  "title": "Integration Techniques Flashcards",
  "content": {
    "title": "Integration Techniques Flashcards",
    "cards": [
      {
        "front": "What is u-substitution?",
        "back": "A technique for simplifying integrals by substituting a variable u for a function",
        "category": "Definitions"
      },
      {
        "front": "Formula: ∫f(g(x))g'(x)dx = ?",
        "back": "∫f(u)du where u = g(x)",
        "category": "Formulas"
      },
      {
        "front": "When should you use u-substitution?",
        "back": "When you can identify a function and its derivative in the integrand",
        "category": "Application"
      }
    ]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T15:20:00Z",
  "updated_at": "2025-01-15T15:20:00Z"
}
```

---

### 4. Generate Lesson Plan

```http
POST /v1/generate/lesson-plan
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "duration": 50,
  "grade_level": "11-12",
  "learning_objectives": [
    "Apply u-substitution to solve integrals",
    "Identify when u-substitution is appropriate"
  ],
  "custom_instructions": "Include group work and technology integration"
}
```

**Parameters:**
- `duration` (optional, default: 45) - Lesson duration in minutes
- `grade_level` (optional) - Target grade level
- `learning_objectives` (optional) - Specific objectives to include

**Response (200):**
```json
{
  "id": "content_6f7g8h9i0j1k",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "lesson_plan",
  "title": "Integration Techniques - Lesson Plan",
  "content": {
    "title": "U-Substitution Integration Lesson Plan",
    "duration": 50,
    "objectives": [
      "Students will apply u-substitution to solve indefinite integrals",
      "Students will identify appropriate substitutions",
      "Students will explain when u-substitution is the best technique"
    ],
    "materials": [
      "Graphing calculator or Desmos",
      "Practice worksheet",
      "Whiteboard and markers"
    ],
    "sections": [
      {
        "title": "Warm-Up Review (5 minutes)",
        "duration": 5,
        "activities": [
          "Review basic integration rules",
          "Quick derivatives practice"
        ],
        "notes": "Activate prior knowledge of derivatives and basic integrals"
      },
      {
        "title": "Introduction to U-Substitution (10 minutes)",
        "duration": 10,
        "activities": [
          "Present the u-substitution formula",
          "Work through a simple example together",
          "Discuss how to choose u"
        ],
        "notes": "Use think-aloud strategy to model problem-solving"
      },
      {
        "title": "Guided Practice (15 minutes)",
        "duration": 15,
        "activities": [
          "Students work in pairs on 3 problems",
          "Teacher circulates to provide support",
          "Volunteers share solutions on board"
        ],
        "notes": "Monitor for common errors in choosing u or computing du"
      },
      {
        "title": "Independent Practice (15 minutes)",
        "duration": 15,
        "activities": [
          "Students complete worksheet individually",
          "Use graphing calculator to verify answers"
        ],
        "notes": "This assesses individual understanding"
      },
      {
        "title": "Closure and Exit Ticket (5 minutes)",
        "duration": 5,
        "activities": [
          "Summarize key steps of u-substitution",
          "Students complete exit ticket"
        ],
        "notes": "Exit ticket question: Solve ∫2x(x²+1)³dx"
      }
    ],
    "assessment": "Exit ticket and worksheet completion",
    "homework": "Textbook problems 5.1-5.15 (odd numbers)",
    "differentiation": [
      "Support: Provide substitution hints for struggling students",
      "Extension: Challenge advanced students with trigonometric substitutions"
    ]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T15:30:00Z",
  "updated_at": "2025-01-15T15:30:00Z"
}
```

---

### 5. Generate Slides (LaTeX Beamer)

```http
POST /v1/generate/slides
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_slides": 15,
  "style": "professional",
  "custom_instructions": "Include visual examples and step-by-step solutions"
}
```

**Parameters:**
- `num_slides` (optional, default: 10) - Approximate number of slides
- `style` (optional): "professional", "academic", "minimal"

**Response (200):**
```json
{
  "id": "content_7g8h9i0j1k2l",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "slides",
  "title": "Integration Techniques - Slides",
  "content": {
    "title": "U-Substitution in Integration",
    "subtitle": "Advanced Calculus Techniques",
    "author": "Jane Smith",
    "slides": [
      {
        "title": "Learning Objectives",
        "content": [
          "Understand the concept of u-substitution",
          "Apply u-substitution to solve integrals",
          "Identify when u-substitution is appropriate"
        ],
        "notes": "Start with objectives to set expectations"
      },
      {
        "title": "What is U-Substitution?",
        "content": [
          "A method for simplifying complex integrals",
          "Based on the chain rule in reverse",
          "Formula: $\\int f(g(x))g'(x)dx = \\int f(u)du$ where $u = g(x)$"
        ],
        "notes": "Emphasize the connection to chain rule"
      },
      {
        "title": "Step-by-Step Process",
        "content": [
          "1. Choose $u = g(x)$ (look for composite functions)",
          "2. Compute $du = g'(x)dx$",
          "3. Rewrite the integral in terms of $u$ and $du$",
          "4. Integrate with respect to $u$",
          "5. Substitute back to get answer in terms of $x$"
        ],
        "notes": "Walk through each step with emphasis"
      }
    ],
    "latex_validated": true,
    "compile_errors": []
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T15:40:00Z",
  "updated_at": "2025-01-15T15:40:00Z"
}
```

**Note:** Slides are automatically validated for LaTeX syntax and AI attempts to fix any compilation errors.

---

### 6. Generate Learning Objectives

```http
POST /v1/generate/learning-objectives
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "grade_level": "11-12",
  "num_objectives": 5,
  "custom_instructions": "Follow SMART criteria and Bloom's taxonomy"
}
```

**Parameters:**
- `num_objectives` (optional, default: 5) - Number of objectives
- `grade_level` (optional) - Target grade level

**Response (200):**
```json
{
  "id": "content_8h9i0j1k2l3m",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "learning_objectives",
  "title": "Learning Objectives",
  "content": {
    "title": "Learning Objectives - U-Substitution",
    "objectives": [
      {
        "objective": "Students will be able to apply u-substitution to solve indefinite integrals with 90% accuracy",
        "bloom_level": "Apply",
        "success_criteria": [
          "Correctly identify when u-substitution is appropriate",
          "Properly choose u and compute du",
          "Successfully integrate and substitute back to x"
        ]
      },
      {
        "objective": "Students will be able to explain the relationship between u-substitution and the chain rule",
        "bloom_level": "Understand",
        "success_criteria": [
          "Verbally describe how u-substitution reverses the chain rule",
          "Provide examples demonstrating this relationship"
        ]
      },
      {
        "objective": "Students will be able to evaluate definite integrals using u-substitution with changed bounds",
        "bloom_level": "Apply",
        "success_criteria": [
          "Transform both the integrand and bounds to u",
          "Compute the definite integral without substituting back"
        ]
      }
    ]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T15:50:00Z",
  "updated_at": "2025-01-15T15:50:00Z"
}
```

---

### 7. Generate Vocabulary List

```http
POST /v1/generate/vocabulary
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_words": 15,
  "difficulty_level": "intermediate",
  "custom_instructions": "Include mathematical notation and examples"
}
```

**Parameters:**
- `num_words` (optional, default: 15) - Number of vocabulary terms
- `difficulty_level` (optional): "beginner", "intermediate", "advanced"

**Response (200):**
```json
{
  "id": "content_9i0j1k2l3m4n",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "vocabulary",
  "title": "Vocabulary List",
  "content": {
    "title": "Integration Techniques Vocabulary",
    "words": [
      {
        "term": "U-Substitution",
        "definition": "An integration technique that simplifies integrals by substituting a new variable u for a composite function",
        "example": "For ∫2x(x²+1)³dx, let u = x² + 1",
        "part_of_speech": "noun"
      },
      {
        "term": "Integrand",
        "definition": "The function being integrated in an integral expression",
        "example": "In ∫x²dx, the integrand is x²",
        "part_of_speech": "noun"
      },
      {
        "term": "Composite Function",
        "definition": "A function formed by applying one function to the result of another",
        "example": "f(g(x)) where f and g are functions",
        "part_of_speech": "noun"
      }
    ]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T16:00:00Z",
  "updated_at": "2025-01-15T16:00:00Z"
}
```

---

### 8. Generate Worksheet

```http
POST /v1/generate/worksheet
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_questions": 10,
  "question_types": ["short_answer", "problem_solving", "fill_blank"],
  "include_answer_key": true,
  "custom_instructions": "Mix computational and conceptual problems"
}
```

**Parameters:**
- `num_questions` (optional, default: 10) - Number of questions
- `question_types` (optional): ["short_answer", "problem_solving", "fill_blank", "true_false"]
- `include_answer_key` (optional, default: true) - Include solutions

**Response (200):**
```json
{
  "id": "content_0j1k2l3m4n5o",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "worksheet",
  "title": "U-Substitution Practice Worksheet",
  "content": {
    "title": "U-Substitution Practice Worksheet",
    "instructions": "Show all work. You may use a calculator for arithmetic.",
    "questions": [
      {
        "number": 1,
        "type": "problem_solving",
        "question": "Evaluate: ∫2x(x²+1)⁴dx",
        "points": 5,
        "answer": "(x²+1)⁵/5 + C",
        "solution_steps": [
          "Let u = x² + 1",
          "Then du = 2x dx",
          "Integral becomes ∫u⁴ du",
          "= u⁵/5 + C",
          "= (x²+1)⁵/5 + C"
        ]
      },
      {
        "number": 2,
        "type": "short_answer",
        "question": "Explain in your own words when u-substitution is the appropriate integration technique.",
        "points": 3,
        "answer": "U-substitution is appropriate when the integrand contains a composite function and the derivative of the inner function (or a constant multiple) is also present in the integrand."
      }
    ],
    "answer_key": {
      "included": true,
      "solutions_detail": "full"
    }
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T16:10:00Z",
  "updated_at": "2025-01-15T16:10:00Z"
}
```

---

### 9. Generate Classroom Activities

```http
POST /v1/generate/activities
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_activities": 5,
  "activity_types": ["individual", "group", "hands_on"],
  "duration_per_activity": 15,
  "custom_instructions": "Include technology integration and collaborative work"
}
```

**Parameters:**
- `num_activities` (optional, default: 5) - Number of activities
- `activity_types` (optional): ["individual", "group", "hands_on", "technology"]
- `duration_per_activity` (optional) - Minutes per activity

**Response (200):**
```json
{
  "id": "content_1k2l3m4n5o6p",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "activities",
  "title": "Classroom Activities",
  "content": {
    "title": "U-Substitution Learning Activities",
    "activities": [
      {
        "name": "Integration Detective",
        "type": "group",
        "duration": 15,
        "description": "Students work in groups to identify which integrals can be solved with u-substitution",
        "materials": ["Worksheet with 20 integrals", "Markers", "Chart paper"],
        "instructions": [
          "Divide class into groups of 3-4 students",
          "Give each group a list of 20 different integrals",
          "Groups categorize integrals: u-substitution, other technique, or unsure",
          "Groups present their reasoning for 2-3 integrals",
          "Class discusses any disagreements"
        ],
        "learning_goals": "Develop pattern recognition for when u-substitution applies"
      },
      {
        "name": "Desmos Integration Explorer",
        "type": "technology",
        "duration": 15,
        "description": "Use Desmos to visualize integrals before and after substitution",
        "materials": ["Computers or tablets", "Desmos.com access"],
        "instructions": [
          "Open Desmos graphing calculator",
          "Graph the original integrand",
          "Graph the transformed integrand after substitution",
          "Compare the area under both curves",
          "Verify that the antiderivatives are equivalent"
        ],
        "learning_goals": "Understand that substitution doesn't change the area, just the representation"
      },
      {
        "name": "U-Substitution Race",
        "type": "individual",
        "duration": 10,
        "description": "Timed practice solving 5 integrals using u-substitution",
        "materials": ["Practice sheet", "Timer"],
        "instructions": [
          "Students receive 5 integrals of varying difficulty",
          "Set timer for 10 minutes",
          "Students solve as many as possible",
          "Self-check using answer key",
          "Students identify which problems they struggled with"
        ],
        "learning_goals": "Build fluency and speed with u-substitution"
      }
    ]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T16:20:00Z",
  "updated_at": "2025-01-15T16:20:00Z"
}
```

---

### 10. Generate Exit Ticket

```http
POST /v1/generate/exit-ticket
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "import_id": "import_2b3c4d5e6f7g",
  "num_questions": 3,
  "question_format": "short_answer",
  "custom_instructions": "Focus on key concepts and common misconceptions"
}
```

**Parameters:**
- `num_questions` (optional, default: 3) - Number of questions (typically 2-5)
- `question_format` (optional): "short_answer", "multiple_choice", "problem_solving"

**Response (200):**
```json
{
  "id": "content_2l3m4n5o6p7q",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "content_type": "exit_ticket",
  "title": "Exit Ticket",
  "content": {
    "title": "Exit Ticket - U-Substitution",
    "instructions": "Answer all questions before leaving. This should take 3-5 minutes.",
    "questions": [
      {
        "question": "Evaluate: ∫3x²(x³+5)²dx",
        "type": "problem_solving"
      },
      {
        "question": "What is the most important thing to look for when deciding if u-substitution will work?",
        "type": "short_answer"
      },
      {
        "question": "On a scale of 1-5, how confident are you with u-substitution? What's one thing you still find confusing?",
        "type": "reflection"
      }
    ]
  },
  "is_saved": 0,
  "version": 1,
  "created_at": "2025-01-15T16:30:00Z",
  "updated_at": "2025-01-15T16:30:00Z"
}
```

---

### Content Management Operations

#### Regenerate Content with Modifications

```http
POST /v1/generate/regenerate
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "content_id": "content_4d5e6f7g8h9i",
  "modifications": "Make the quiz harder and add more application-based questions"
}
```

**Response (200):** Returns updated content object with incremented version number

#### List All Generated Content for a Lesson

```http
GET /v1/generate/lesson/lesson_d4e5f6g7h8i9/content?page=1&limit=20
Authorization: Bearer <access_token>
```

**Response (200):**
```json
{
  "data": [
    {
      "id": "content_3c4d5e6f7g8h",
      "lesson_id": "lesson_d4e5f6g7h8i9",
      "content_type": "summary",
      "title": "Integration Techniques - Summary",
      "is_saved": 1,
      "version": 1,
      "created_at": "2025-01-15T15:00:00Z",
      "updated_at": "2025-01-15T15:00:00Z"
    }
  ],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 8,
    "total_pages": 1,
    "has_next": false,
    "has_prev": false
  }
}
```

#### Get Specific Content

```http
GET /v1/generate/content/content_4d5e6f7g8h9i
Authorization: Bearer <access_token>
```

**Response (200):** Returns complete content object

#### Update Content (Save from Preview)

```http
PATCH /v1/generate/content/content_4d5e6f7g8h9i
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "title": "Updated Quiz Title",
  "is_saved": 1,
  "content": {
    "title": "Updated Quiz Title",
    "questions": [...]
  }
}
```

**Response (200):** Returns updated content object

#### Delete Content

```http
DELETE /v1/generate/content/content_4d5e6f7g8h9i
Authorization: Bearer <access_token>
```

**Response (204):** No content

#### Get Content Version History

```http
GET /v1/generate/content/content_4d5e6f7g8h9i/versions
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  {
    "id": "version_abc123def456",
    "content_id": "content_4d5e6f7g8h9i",
    "version_number": 2,
    "content": {
      "title": "Modified quiz with harder questions",
      "questions": [...]
    },
    "change_description": "Made questions more difficult",
    "created_at": "2025-01-15T16:00:00Z"
  },
  {
    "id": "version_def456ghi789",
    "content_id": "content_4d5e6f7g8h9i",
    "version_number": 1,
    "content": {
      "title": "Original quiz",
      "questions": [...]
    },
    "change_description": "Initial version",
    "created_at": "2025-01-15T15:10:00Z"
  }
]
```

#### Restore Content to Previous Version

```http
POST /v1/generate/content/content_4d5e6f7g8h9i/restore/1
Authorization: Bearer <access_token>
```

**Response (200):** Returns content restored to version 1 (creates a new version)

---

## Student Progress & Logging

> **Note:** The current system is designed for teacher content creation and does not include explicit student progress tracking APIs. Below are recommendations for integration with your customer dashboard.

### Current Capabilities

The platform provides foundational data that can be used for progress tracking:

1. **Content Creation Timestamps** - Track when materials are created
2. **Content Versioning** - Monitor content iterations and improvements
3. **Import Status** - Track processing of uploaded materials
4. **Generated Content** - All quizzes, worksheets, and assessments available for student use

### Recommended Integration for Student Progress

To implement student progress tracking in your customer dashboard, you can:

#### 1. Track Content Assignment
Use the content IDs to assign materials to students:
```json
{
  "student_id": "student_123",
  "content_id": "content_4d5e6f7g8h9i",
  "content_type": "quiz",
  "assigned_at": "2025-01-15T17:00:00Z",
  "due_date": "2025-01-22T23:59:59Z"
}
```

#### 2. Log Student Activity
Track when students access and complete content:
```json
{
  "student_id": "student_123",
  "content_id": "content_4d5e6f7g8h9i",
  "activity_type": "started|completed|submitted",
  "timestamp": "2025-01-16T14:30:00Z",
  "time_spent_seconds": 1200
}
```

#### 3. Store Quiz Results
For generated quizzes, store student responses and scores:
```json
{
  "student_id": "student_123",
  "content_id": "content_4d5e6f7g8h9i",
  "quiz_attempt": 1,
  "responses": [0, 1, 2, 0, 3, 1, 2, 0, 1, 3],
  "score": 8,
  "total_questions": 10,
  "percentage": 80,
  "submitted_at": "2025-01-16T15:30:00Z"
}
```

#### 4. Track Learning Progress
Monitor progress through lessons and courses:
```json
{
  "student_id": "student_123",
  "course_id": "course_b2c3d4e5f6g7",
  "lesson_id": "lesson_d4e5f6g7h8i9",
  "status": "not_started|in_progress|completed",
  "progress_percentage": 60,
  "last_accessed": "2025-01-16T15:30:00Z"
}
```

### Export Student Data

Use the JSON export functionality to extract quiz and worksheet data:

```http
POST /v1/export/export
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "content_id": "content_4d5e6f7g8h9i",
  "export_format": "json"
}
```

This provides structured data (questions, answers, scoring rubrics) that can be used in your student assessment system.

---

## Intelligence & Analytics APIs

Analyze and aggregate educational data across your content library.

### Search APIs

#### Global Search

```http
GET /v1/search/search?q=calculus&search_type=all&page=1&limit=20
Authorization: Bearer <access_token>
```

**Query Parameters:**
- `q` (required) - Search query
- `search_type` (optional) - "all", "courses", "lessons", "content" (default: "all")
- `page` (optional) - Page number (default: 1)
- `limit` (optional) - Results per page (default: 20, max: 100)

**Response (200) - search_type=all:**
```json
{
  "courses": [
    {
      "id": "course_b2c3d4e5f6g7",
      "title": "Advanced Mathematics",
      "description": "Calculus and advanced algebra concepts",
      "subject": "Mathematics",
      "grade_level": "11-12",
      "lesson_count": 12
    }
  ],
  "lessons": [
    {
      "id": "lesson_d4e5f6g7h8i9",
      "course_id": "course_b2c3d4e5f6g7",
      "title": "Integration Techniques",
      "description": "Learn various methods of integration",
      "topics": ["integration", "u-substitution"],
      "content_count": 5
    }
  ],
  "content": [
    {
      "id": "content_3c4d5e6f7g8h",
      "lesson_id": "lesson_d4e5f6g7h8i9",
      "content_type": "summary",
      "title": "Integration Techniques - Summary",
      "is_saved": 1
    }
  ]
}
```

**Response (200) - search_type=courses:**
```json
{
  "data": [...courses...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 15,
    "total_pages": 1,
    "has_next": false,
    "has_prev": false
  }
}
```

#### Search by Specific Type

```http
GET /v1/search/search?q=python&search_type=lessons
Authorization: Bearer <access_token>
```

Returns paginated results for the specified type only.

---

### Aggregation & Insights APIs

#### Get All Tags

```http
GET /v1/search/tags
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  "programming",
  "python",
  "beginner",
  "math",
  "calculus",
  "advanced",
  "algebra",
  "web-development"
]
```

**Use Case:** Tag cloud visualization, filtering, curriculum analysis

#### Get All Topics

```http
GET /v1/search/topics
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  "variables",
  "functions",
  "loops",
  "integration",
  "derivatives",
  "u-substitution",
  "data types",
  "algorithms"
]
```

**Use Case:** Topic frequency analysis, curriculum gap identification

#### Get All Subjects

```http
GET /v1/search/subjects
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  "Computer Science",
  "Mathematics",
  "English",
  "Science",
  "History",
  "Physics"
]
```

**Use Case:** Subject distribution charts, multi-subject curriculum planning

#### Get All Grade Levels

```http
GET /v1/search/grade-levels
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  "6-8",
  "9-10",
  "11-12",
  "College",
  "Adult Education"
]
```

**Use Case:** Grade level distribution, content targeting

---

### Analytics Use Cases

#### 1. Curriculum Coverage Analysis
Combine tags, topics, and subjects data to identify:
- Most covered topics
- Underrepresented areas
- Topic distribution across grade levels
- Subject matter balance

#### 2. Content Generation Metrics
Use content listing APIs to analyze:
- Most generated content types (quiz vs. summary vs. slides)
- Content generation trends over time
- Popular lesson topics
- Teacher content preferences

#### 3. Course Performance Insights
Track across courses:
- Number of lessons per course
- Average content items per lesson
- Course completion rates (when integrated with student data)
- Most accessed courses

#### 4. Search Analytics
Monitor search queries to understand:
- Popular search terms
- Content discovery patterns
- Content gaps (high searches, low results)

---

## Export & Integration APIs

Export generated content in multiple formats for use in other systems.

### Export Formats

| Format | Description | Use Case |
|--------|-------------|----------|
| **PDF** | Formatted document ready to print | Handouts, worksheets, study guides |
| **DOCX** | Microsoft Word document | Editable content for teachers |
| **JSON** | Structured data format | System integration, data analysis |

### Create Export Job

```http
POST /v1/export/export
Authorization: Bearer <access_token>
Content-Type: application/json

{
  "content_id": "content_4d5e6f7g8h9i",
  "export_format": "pdf"
}
```

**Supported Formats:** `pdf`, `docx`, `json`

**Response (201):**
```json
{
  "id": "export_3m4n5o6p7q8r",
  "user_id": "user_94dce572a034",
  "content_id": "content_4d5e6f7g8h9i",
  "export_format": "pdf",
  "status": "completed",
  "file_path": "/exports/export_3m4n5o6p7q8r.pdf",
  "error_message": null,
  "created_at": "2025-01-15T17:00:00Z",
  "completed_at": "2025-01-15T17:00:15Z",
  "expires_at": "2025-01-22T17:00:15Z"
}
```

**Status Values:**
- `pending` - Export job queued
- `processing` - Currently generating file
- `completed` - File ready for download
- `failed` - Export failed (see error_message)

### Get Export Status

```http
GET /v1/export/export/export_3m4n5o6p7q8r
Authorization: Bearer <access_token>
```

**Response (200):** Returns export job object

### Download Exported File

```http
GET /v1/export/export/export_3m4n5o6p7q8r/download
Authorization: Bearer <access_token>
```

**Response (200):**
- **Content-Type:** `application/pdf`, `application/vnd.openxmlformats-officedocument.wordprocessingml.document`, or `application/json`
- **Body:** Binary file data

**Note:** Download links expire after 7 days

### List All Exports

```http
GET /v1/export/exports
Authorization: Bearer <access_token>
```

**Response (200):**
```json
[
  {
    "id": "export_3m4n5o6p7q8r",
    "content_id": "content_4d5e6f7g8h9i",
    "export_format": "pdf",
    "status": "completed",
    "created_at": "2025-01-15T17:00:00Z",
    "expires_at": "2025-01-22T17:00:15Z"
  }
]
```

### Delete Export

```http
DELETE /v1/export/export/export_3m4n5o6p7q8r
Authorization: Bearer <access_token>
```

**Response (204):** No content (deletes both database record and file)

---

## Technical Reference

### ID Pattern Standards

All resource IDs follow a consistent pattern: `{resource_type}_{random_alphanumeric}`

| Resource | Pattern | Example |
|----------|---------|---------|
| User | `user_{12 chars}` | `user_94dce572a034` |
| Course | `course_{12 chars}` | `course_a1b2c3d4e5f6` |
| Lesson | `lesson_{12 chars}` | `lesson_f6e5d4c3b2a1` |
| Import Job | `import_{12 chars}` | `import_1a2b3c4d5e6f` |
| Generated Content | `content_{12 chars}` | `content_9z8y7x6w5v4u` |
| Content Version | `version_{12 chars}` | `version_abc123def456` |
| Export Job | `export_{12 chars}` | `export_3m4n5o6p7q8r` |

### Database Schema

#### users
```sql
- id (PK): VARCHAR (user_xxxxx)
- email: VARCHAR (unique, indexed)
- hashed_password: VARCHAR (bcrypt)
- first_name: VARCHAR
- last_name: VARCHAR
- role: VARCHAR (default: "teacher")
- email_verified: BOOLEAN (default: false)
- verification_code: VARCHAR (6 digits)
- verification_code_expires: DATETIME
- preferences: JSON
- created_at: DATETIME
- updated_at: DATETIME
```

#### courses
```sql
- id (PK): VARCHAR (course_xxxxx)
- user_id (FK): VARCHAR → users.id
- title: VARCHAR (indexed)
- description: TEXT
- subject: VARCHAR (indexed)
- grade_level: VARCHAR (indexed)
- tags: JSON (array)
- created_at: DATETIME
- updated_at: DATETIME
- deleted_at: DATETIME (null = active)
```

#### lessons
```sql
- id (PK): VARCHAR (lesson_xxxxx)
- course_id (FK): VARCHAR → courses.id
- title: VARCHAR (indexed)
- description: TEXT
- order: INTEGER (for sorting)
- topics: JSON (array)
- created_at: DATETIME
- updated_at: DATETIME
```

#### import_jobs
```sql
- id (PK): VARCHAR (import_xxxxx)
- lesson_id (FK): VARCHAR → lessons.id
- file_name: VARCHAR
- file_size: BIGINT (bytes)
- file_type: VARCHAR (extension)
- media_type: VARCHAR (document|video|audio|image|markdown)
- status: VARCHAR (pending|processing|completed|failed)
- extracted_text: TEXT
- topics: JSON (array)
- key_points: JSON (array)
- duration: INTEGER (seconds, for audio/video)
- language_detected: VARCHAR
- transcription_service: VARCHAR (openai|deepgram)
- transcription_confidence: FLOAT
- audio_url: VARCHAR (for video files)
- ai_summary: TEXT
- main_concepts: JSON (array)
- content_structure: JSON
- educational_metadata: JSON
- processing_tokens_used: INTEGER
- error_message: TEXT
- created_at: DATETIME
- completed_at: DATETIME
```

#### generated_content
```sql
- id (PK): VARCHAR (content_xxxxx)
- lesson_id (FK): VARCHAR → lessons.id
- content_type: VARCHAR (summary|quiz|flashcards|etc.)
- title: VARCHAR
- content: JSON (structured content)
- source_text: TEXT
- is_saved: INTEGER (0=preview, 1=saved)
- version: INTEGER (starts at 1)
- created_at: DATETIME
- updated_at: DATETIME
```

#### content_versions
```sql
- id (PK): VARCHAR (version_xxxxx)
- content_id (FK): VARCHAR → generated_content.id
- version_number: INTEGER
- content: JSON
- change_description: TEXT
- created_at: DATETIME
```

#### export_jobs
```sql
- id (PK): VARCHAR (export_xxxxx)
- user_id (FK): VARCHAR → users.id
- content_id (FK): VARCHAR → generated_content.id
- export_format: VARCHAR (pdf|docx|json)
- status: VARCHAR (pending|processing|completed|failed)
- file_path: VARCHAR
- error_message: TEXT
- created_at: DATETIME
- completed_at: DATETIME
- expires_at: DATETIME (7 days from completion)
```

#### refresh_tokens
```sql
- id (PK): INTEGER (auto-increment)
- token: VARCHAR (unique, indexed)
- user_id (FK): VARCHAR → users.id
- expires_at: DATETIME (30 days)
- created_at: DATETIME
```

### Pagination Standard

All list endpoints support pagination with consistent parameters:

**Request Parameters:**
- `page` (integer, default: 1) - Page number (1-indexed)
- `limit` (integer, default: 20, max: 100) - Items per page

**Response Format:**
```json
{
  "data": [...],
  "pagination": {
    "page": 1,
    "limit": 20,
    "total": 100,
    "total_pages": 5,
    "has_next": true,
    "has_prev": false
  }
}
```

### Timestamp Format

All timestamps use ISO 8601 format in UTC:
```
2025-01-15T17:00:00Z
```

### Authentication Header Format

All authenticated requests must include:
```
Authorization: Bearer eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...
```

---

## Error Handling

### Standard Error Response

```json
{
  "detail": "Error message describing what went wrong"
}
```

For validation errors (422):
```json
{
  "detail": [
    {
      "loc": ["body", "email"],
      "msg": "field required",
      "type": "value_error.missing"
    }
  ]
}
```

### HTTP Status Codes

| Code | Meaning | Common Causes |
|------|---------|---------------|
| **200** | OK | Successful GET, PATCH, POST request |
| **201** | Created | Successful resource creation |
| **204** | No Content | Successful deletion |
| **400** | Bad Request | Invalid request format, missing parameters |
| **401** | Unauthorized | Missing, invalid, or expired access token |
| **403** | Forbidden | Valid token but insufficient permissions |
| **404** | Not Found | Resource doesn't exist or was deleted |
| **409** | Conflict | Duplicate resource (e.g., email already exists) |
| **422** | Unprocessable Entity | Validation error in request body |
| **429** | Too Many Requests | Rate limit exceeded (100 requests/hour for generation) |
| **500** | Internal Server Error | Server error, contact support |

### Common Error Scenarios

#### 1. Token Expired (401)
```json
{
  "detail": "Token has expired"
}
```
**Solution:** Use refresh token to get new access token

#### 2. Rate Limit Exceeded (429)
```json
{
  "detail": "Rate limit exceeded. Maximum 100 requests per hour."
}
```
**Solution:** Wait until rate limit window resets (60 minute rolling window)

#### 3. Validation Error (422)
```json
{
  "detail": [
    {
      "loc": ["body", "email"],
      "msg": "value is not a valid email address",
      "type": "value_error.email"
    }
  ]
}
```
**Solution:** Fix the invalid field in request body

#### 4. Resource Not Found (404)
```json
{
  "detail": "Course not found"
}
```
**Solution:** Verify the resource ID is correct and resource hasn't been deleted

#### 5. Insufficient Permissions (403)
```json
{
  "detail": "You don't have permission to access this resource"
}
```
**Solution:** Ensure user owns the resource or has appropriate permissions

---

## Rate Limiting

### Generation Endpoints

AI content generation endpoints have rate limiting to prevent abuse:

- **Limit:** 100 requests per hour per user
- **Window:** 60-minute rolling window
- **Applies to:** All `/v1/generate/*` endpoints

### Rate Limit Headers

Responses include rate limit information:
```
X-RateLimit-Limit: 100
X-RateLimit-Remaining: 87
X-RateLimit-Reset: 1705338000
```

### Rate Limit Response

When limit is exceeded (429):
```json
{
  "detail": "Rate limit exceeded. Maximum 100 requests per hour.",
  "retry_after": 1800
}
```
**retry_after:** Seconds until rate limit resets

### Best Practices

1. **Cache Results:** Store generated content in your database
2. **Batch Operations:** Generate multiple content types in sequence within a single session
3. **Monitor Headers:** Track remaining requests using response headers
4. **Implement Backoff:** Wait and retry when approaching limit
5. **Use Context Mode:** Use `import_id` instead of `source_text` to reduce token usage

---

## Integration Guide

### Authentication Flow

```mermaid
sequenceDiagram
    Client->>API: POST /auth/register or /auth/login
    API->>Client: access_token + refresh_token
    Client->>API: GET /courses (Authorization: Bearer <access_token>)
    API->>Client: 200 OK with data
    Note over Client,API: After 60 minutes...
    Client->>API: Request with expired token
    API->>Client: 401 Unauthorized
    Client->>API: POST /auth/refresh with refresh_token
    API->>Client: New access_token + refresh_token
    Client->>API: Retry original request with new token
    API->>Client: 200 OK with data
```

### Recommended Workflow 1: Create Course with Content

```javascript
// 1. Create a course
const courseResponse = await axios.post('/v1/courses', {
  title: 'Introduction to Python',
  description: 'Learn Python programming',
  subject: 'Computer Science',
  grade_level: '9-12',
  tags: ['programming', 'python']
}, {
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

const courseId = courseResponse.data.id;

// 2. Create lessons
const lessonResponse = await axios.post(`/v1/courses/${courseId}/lessons`, {
  title: 'Variables and Data Types',
  description: 'Learn about Python variables',
  order: 1,
  topics: ['variables', 'data types']
}, {
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

const lessonId = lessonResponse.data.id;

// 3. Upload content
const formData = new FormData();
formData.append('lesson_id', lessonId);
formData.append('file', fileBlob);

const importResponse = await axios.post('/v1/import/upload', formData, {
  headers: {
    'Authorization': `Bearer ${accessToken}`,
    'Content-Type': 'multipart/form-data'
  }
});

const importId = importResponse.data.id;

// 4. Generate multiple content types
const quizResponse = await axios.post('/v1/generate/quiz', {
  lesson_id: lessonId,
  import_id: importId,
  num_questions: 10,
  difficulty: 'medium'
}, {
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

const flashcardsResponse = await axios.post('/v1/generate/flashcards', {
  lesson_id: lessonId,
  import_id: importId,
  num_cards: 20
}, {
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

// 5. Save content (move from preview to saved)
await axios.patch(`/v1/generate/content/${quizResponse.data.id}`, {
  is_saved: 1
}, {
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

// 6. Export as PDF
const exportResponse = await axios.post('/v1/export/export', {
  content_id: quizResponse.data.id,
  export_format: 'pdf'
}, {
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

// 7. Download the PDF
const pdfBlob = await axios.get(
  `/v1/export/export/${exportResponse.data.id}/download`,
  {
    headers: { 'Authorization': `Bearer ${accessToken}` },
    responseType: 'blob'
  }
);
```

### Recommended Workflow 2: Search and Analytics

```javascript
// 1. Global search
const searchResults = await axios.get('/v1/search/search', {
  params: {
    q: 'calculus',
    search_type: 'all',
    page: 1,
    limit: 20
  },
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

// 2. Get analytics data
const [tags, topics, subjects, gradeLevels] = await Promise.all([
  axios.get('/v1/search/tags', {
    headers: { 'Authorization': `Bearer ${accessToken}` }
  }),
  axios.get('/v1/search/topics', {
    headers: { 'Authorization': `Bearer ${accessToken}` }
  }),
  axios.get('/v1/search/subjects', {
    headers: { 'Authorization': `Bearer ${accessToken}` }
  }),
  axios.get('/v1/search/grade-levels', {
    headers: { 'Authorization': `Bearer ${accessToken}` }
  })
]);

// 3. Analyze curriculum coverage
const tagFrequency = tags.data.reduce((acc, tag) => {
  acc[tag] = (acc[tag] || 0) + 1;
  return acc;
}, {});

// 4. Export aggregated data
const allCoursesResponse = await axios.get('/v1/courses', {
  params: { limit: 100 },
  headers: { 'Authorization': `Bearer ${accessToken}` }
});

const analyticsExport = {
  total_courses: allCoursesResponse.data.pagination.total,
  subjects: subjects.data,
  grade_levels: gradeLevels.data,
  popular_topics: topics.data.slice(0, 10),
  tag_frequency: tagFrequency
};
```

### Token Management Best Practices

```javascript
// Axios interceptor for automatic token refresh
axios.interceptors.response.use(
  response => response,
  async error => {
    const originalRequest = error.config;

    // If 401 and haven't retried yet
    if (error.response?.status === 401 && !originalRequest._retry) {
      originalRequest._retry = true;

      try {
        // Refresh the token
        const refreshResponse = await axios.post('/v1/auth/refresh', {
          refresh_token: localStorage.getItem('refresh_token')
        });

        const { token, refresh_token } = refreshResponse.data;

        // Store new tokens
        localStorage.setItem('access_token', token);
        localStorage.setItem('refresh_token', refresh_token);

        // Retry original request with new token
        originalRequest.headers['Authorization'] = `Bearer ${token}`;
        return axios(originalRequest);
      } catch (refreshError) {
        // Refresh failed, redirect to login
        window.location.href = '/login';
        return Promise.reject(refreshError);
      }
    }

    return Promise.reject(error);
  }
);
```

### Error Handling Best Practices

```javascript
async function generateQuiz(lessonId, importId) {
  try {
    const response = await axios.post('/v1/generate/quiz', {
      lesson_id: lessonId,
      import_id: importId,
      num_questions: 10
    }, {
      headers: { 'Authorization': `Bearer ${accessToken}` }
    });

    return response.data;
  } catch (error) {
    if (error.response) {
      // Server responded with error
      switch (error.response.status) {
        case 400:
          console.error('Invalid request:', error.response.data.detail);
          break;
        case 401:
          console.error('Authentication failed');
          // Token refresh handled by interceptor
          break;
        case 404:
          console.error('Lesson not found');
          break;
        case 429:
          console.error('Rate limit exceeded, retry after:',
                        error.response.data.retry_after);
          // Implement backoff
          break;
        case 500:
          console.error('Server error, contact support');
          break;
        default:
          console.error('Unexpected error:', error.response.data);
      }
    } else if (error.request) {
      // Request made but no response
      console.error('Network error, check connection');
    } else {
      // Something else happened
      console.error('Error:', error.message);
    }

    throw error;
  }
}
```

### File Upload Best Practices

```javascript
async function uploadContentFile(lessonId, file, onProgress) {
  const formData = new FormData();
  formData.append('lesson_id', lessonId);
  formData.append('file', file);

  try {
    const response = await axios.post('/v1/import/upload', formData, {
      headers: {
        'Authorization': `Bearer ${accessToken}`,
        'Content-Type': 'multipart/form-data'
      },
      onUploadProgress: (progressEvent) => {
        const percentCompleted = Math.round(
          (progressEvent.loaded * 100) / progressEvent.total
        );
        onProgress(percentCompleted);
      }
    });

    // Poll for processing completion
    const importId = response.data.id;
    await pollImportStatus(importId);

    return response.data;
  } catch (error) {
    console.error('Upload failed:', error);
    throw error;
  }
}

async function pollImportStatus(importId, maxAttempts = 60) {
  for (let i = 0; i < maxAttempts; i++) {
    const response = await axios.get(`/v1/import/import/${importId}`, {
      headers: { 'Authorization': `Bearer ${accessToken}` }
    });

    if (response.data.status === 'completed') {
      return response.data;
    } else if (response.data.status === 'failed') {
      throw new Error(response.data.error_message);
    }

    // Wait 5 seconds before next check
    await new Promise(resolve => setTimeout(resolve, 5000));
  }

  throw new Error('Import processing timeout');
}
```

---

## Best Practices Summary

### 1. Authentication
- ✅ Store tokens securely (never in URL or logs)
- ✅ Implement automatic token refresh
- ✅ Handle 401 errors gracefully
- ✅ Clear tokens on logout

### 2. API Usage
- ✅ Use `import_id` for generation (saves tokens)
- ✅ Cache generated content in your database
- ✅ Respect rate limits (monitor headers)
- ✅ Implement exponential backoff for retries

### 3. Content Management
- ✅ Use preview mode (`is_saved: 0`) before saving
- ✅ Leverage version control for content iterations
- ✅ Export content in appropriate formats
- ✅ Delete unused imports and exports to save space

### 4. Error Handling
- ✅ Implement comprehensive error handling
- ✅ Provide user-friendly error messages
- ✅ Log errors for debugging
- ✅ Implement retry logic for transient failures

### 5. Performance
- ✅ Use pagination for large result sets
- ✅ Implement client-side caching
- ✅ Make parallel requests when possible
- ✅ Monitor API response times

### 6. Student Integration
- ✅ Use content IDs for assignment tracking
- ✅ Export quiz data for assessment systems
- ✅ Track student activity on your side
- ✅ Aggregate progress data for analytics

---

## Support & Resources

### API Documentation
- **Interactive Docs:** `http://localhost:8000/docs` (Swagger UI)
- **Alternative Docs:** `http://localhost:8000/redoc` (ReDoc)

### Rate Limits
- Generation endpoints: 100 requests/hour
- All other endpoints: No limit

### File Size Limits
- Maximum upload size: 100MB per file
- Supported file types: See [Content Import section](#content-import--processing-apis)

### Token Expiration
- Access tokens: 60 minutes
- Refresh tokens: 30 days
- Export downloads: 7 days

### Need Help?
Contact your technical support team or refer to the interactive API documentation at `/docs` for live testing and examples.

---

**End of Documentation**

*Last Updated: January 2025 | Version 1.0.0*
