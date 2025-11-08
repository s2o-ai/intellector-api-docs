# Intellector Analyzer API Documentation

**Version:** 1.0.0
**Last Updated:** November 2025
**Base URL:** `https://api.intellector-analyzer.com/v1`

---

## Table of Contents

1. [Introduction](#introduction)
2. [Authentication](#authentication)
3. [API Conventions](#api-conventions)
4. [Setup & Configuration APIs](#setup--configuration-apis)
   - [Student Management](#student-management)
   - [Course Management](#course-management)
   - [Content Management](#content-management)
5. [Data Collection & Logging APIs](#data-collection--logging-apis)
   - [Activity Event Logging](#activity-event-logging)
   - [Course Progress Tracking](#course-progress-tracking)
6. [Analytics & Intelligence Layer](#analytics--intelligence-layer)
   - [Dashboard Overview](#dashboard-overview)
   - [Student Analytics](#student-analytics)
   - [Content Analytics](#content-analytics)
   - [Engagement Analytics](#engagement-analytics)
   - [Performance Analytics](#performance-analytics)
   - [Cohort Analytics](#cohort-analytics)
7. [Assessment Analytics APIs](#assessment-analytics-apis)
   - [Topic Mastery](#topic-mastery)
   - [Knowledge Gap Analysis](#knowledge-gap-analysis)
   - [Question-Level Analysis](#question-level-analysis)
   - [Performance Matrix](#performance-matrix)
8. [AI-Powered Insights](#ai-powered-insights)
   - [Comprehensive Analysis](#comprehensive-analysis)
   - [Student Notes](#student-notes)
   - [Content Notes](#content-notes)
9. [Reports & Export](#reports--export)
10. [Data Models](#data-models)
11. [Error Handling](#error-handling)
12. [Calculation Methods](#calculation-methods)

---

## Introduction

**Intellector Analyzer** is an advanced EdTech analytics platform that provides AI-driven insights into student performance, learning patterns, and engagement metrics. The platform enables educational institutions to:

- Track student progress across courses and curriculum
- Identify at-risk students early with predictive analytics
- Analyze content effectiveness and difficulty
- Generate actionable recommendations for interventions
- Export comprehensive reports for stakeholders

### Key Features

- **Multi-Level Analytics**: From class-wide overview to individual question analysis
- **AI-Powered Insights**: Automated narratives, root cause analysis, and recommendations
- **Real-Time Tracking**: Log and monitor student activities as they happen
- **Comprehensive Reporting**: CSV and PDF exports with customizable data
- **Risk Assessment**: Automated identification of struggling students
- **Topic Mastery Tracking**: Skill-level progress monitoring

---

## Authentication

All API requests require authentication using JWT (JSON Web Token) bearer tokens.

### Authentication Header

```http
Authorization: Bearer <your_jwt_token>
```

### Obtaining a Token

```http
POST /api/auth/login
Content-Type: application/json

{
  "email": "user@example.com",
  "password": "your_password"
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "token": "eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9...",
    "refreshToken": "refresh_token_here",
    "expiresIn": 86400,
    "user": {
      "id": "user_123",
      "email": "user@example.com",
      "role": "teacher",
      "name": "Jane Smith"
    }
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

### Token Refresh

```http
POST /api/auth/refresh
Content-Type: application/json

{
  "refreshToken": "refresh_token_here"
}
```

---

## API Conventions

### Standard Response Format

All API responses follow a consistent structure:

```json
{
  "success": true,
  "data": { },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

For errors:

```json
{
  "success": false,
  "message": "Error description",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

### HTTP Status Codes

- `200 OK` - Successful GET request
- `201 Created` - Successful POST request
- `400 Bad Request` - Validation error or missing required fields
- `401 Unauthorized` - Invalid or missing authentication token
- `403 Forbidden` - Insufficient permissions
- `404 Not Found` - Resource not found
- `500 Internal Server Error` - Server-side error

### Pagination

List endpoints support pagination using query parameters:

```
?limit=20&offset=0
```

### Filtering & Sorting

List endpoints support filtering and sorting:

```
?sortBy=createdAt&order=desc&status=active
```

### Date Formats

All dates use ISO 8601 format: `2025-11-07T10:00:00.000Z`

---

## Setup & Configuration APIs

These APIs enable you to set up your learning environment by adding students, courses, and curriculum content.

### Student Management

#### Create Student

Add a new student to the system.

```http
POST /api/students
Content-Type: application/json
Authorization: Bearer <token>

{
  "name": "John Doe",
  "email": "john.doe@school.edu",
  "grade": "10",
  "class": "10-A",
  "status": "active"
}
```

**Required Fields:**
- `name` (string): Student's full name
- `email` (string): Unique email address
- `grade` (string): Grade level (e.g., "9", "10", "11", "12")
- `class` (string): Class section (e.g., "10-A", "10-B")

**Optional Fields:**
- `status` (string): "active" or "inactive" (default: "active")

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "student_1730963400000_abc123",
    "name": "John Doe",
    "email": "john.doe@school.edu",
    "grade": "10",
    "class": "10-A",
    "enrolledAt": "2025-11-07T10:00:00.000Z",
    "status": "active"
  },
  "message": "Student created successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### List All Students

Retrieve all students with optional filtering.

```http
GET /api/students?search=john&grade=10&class=10-A&status=active&sortBy=name&order=asc
Authorization: Bearer <token>
```

**Query Parameters:**
- `search` (string): Search by name or email
- `grade` (string): Filter by grade level
- `class` (string): Filter by class section
- `status` (string): Filter by status ("active" or "inactive")
- `sortBy` (string): Sort field (name, email, enrolledAt)
- `order` (string): "asc" or "desc"
- `limit` (number): Results per page (default: 50)
- `offset` (number): Skip results (default: 0)

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "student_123",
      "name": "John Doe",
      "email": "john.doe@school.edu",
      "grade": "10",
      "class": "10-A",
      "enrolledAt": "2025-09-01T00:00:00.000Z",
      "status": "active"
    }
  ],
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Get Single Student

Retrieve detailed information about a specific student.

```http
GET /api/students/{studentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "student_123",
    "name": "John Doe",
    "email": "john.doe@school.edu",
    "grade": "10",
    "class": "10-A",
    "enrolledAt": "2025-09-01T00:00:00.000Z",
    "status": "active"
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Update Student

Update student information.

```http
PUT /api/students/{studentId}
Content-Type: application/json
Authorization: Bearer <token>

{
  "name": "John Updated Doe",
  "class": "10-B",
  "status": "inactive"
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "student_123",
    "name": "John Updated Doe",
    "email": "john.doe@school.edu",
    "grade": "10",
    "class": "10-B",
    "enrolledAt": "2025-09-01T00:00:00.000Z",
    "status": "inactive"
  },
  "message": "Student updated successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Delete Student

Remove a student from the system (also deletes associated activity events).

```http
DELETE /api/students/{studentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "message": "Student deleted successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Get Student Statistics

Retrieve aggregate statistics about all students.

```http
GET /api/students/stats
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "total": 150,
    "active": 142,
    "inactive": 8,
    "byGrade": {
      "9": 38,
      "10": 54,
      "11": 42,
      "12": 16
    },
    "byClass": {
      "9-A": 20,
      "9-B": 18,
      "10-A": 28,
      "10-B": 26,
      "11-A": 21,
      "11-B": 21,
      "12-A": 16
    }
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

---

### Course Management

#### Create Course

Add a new course to the curriculum.

```http
POST /api/courses
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Foundations of Algebra",
  "description": "An introductory course covering fundamental algebraic concepts and problem-solving techniques.",
  "subject": "Mathematics",
  "level": "beginner",
  "totalHours": 60,
  "learningObjectives": [
    "Understand basic algebraic expressions and equations",
    "Solve linear equations and inequalities",
    "Graph linear functions"
  ],
  "prerequisites": []
}
```

**Required Fields:**
- `title` (string): Course name
- `description` (string): Course description
- `subject` (string): Subject area (Mathematics, Science, English, History, etc.)
- `level` (string): "beginner", "intermediate", or "advanced"
- `totalHours` (number): Estimated course duration in hours

**Optional Fields:**
- `learningObjectives` (array): List of learning goals
- `prerequisites` (array): List of prerequisite course IDs

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "course_001",
    "title": "Foundations of Algebra",
    "description": "An introductory course covering fundamental algebraic concepts and problem-solving techniques.",
    "subject": "Mathematics",
    "level": "beginner",
    "totalHours": 60,
    "learningObjectives": [
      "Understand basic algebraic expressions and equations",
      "Solve linear equations and inequalities",
      "Graph linear functions"
    ],
    "prerequisites": [],
    "createdAt": "2025-11-07T10:00:00.000Z"
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### List All Courses

Retrieve all available courses.

```http
GET /api/courses
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "course_001",
      "title": "Foundations of Algebra",
      "description": "An introductory course covering fundamental algebraic concepts...",
      "subject": "Mathematics",
      "level": "beginner",
      "totalHours": 60,
      "learningObjectives": ["..."],
      "prerequisites": [],
      "createdAt": "2025-09-01T00:00:00.000Z"
    }
  ],
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Get Course by ID

Retrieve detailed information about a specific course.

```http
GET /api/courses/{courseId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "course_001",
    "title": "Foundations of Algebra",
    "description": "An introductory course covering fundamental algebraic concepts...",
    "subject": "Mathematics",
    "level": "beginner",
    "totalHours": 60,
    "learningObjectives": [
      "Understand basic algebraic expressions and equations",
      "Solve linear equations and inequalities",
      "Graph linear functions"
    ],
    "prerequisites": [],
    "createdAt": "2025-09-01T00:00:00.000Z"
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Get Course Curriculum

Retrieve all content items (lessons, videos, quizzes) for a course.

```http
GET /api/courses/{courseId}/curriculum
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "content_001",
      "courseId": "course_001",
      "title": "Introduction to Algebra",
      "description": "Learn the basics of algebraic thinking",
      "type": "lesson",
      "difficulty": "beginner",
      "estimatedMinutes": 45,
      "order": 1,
      "skills": ["algebraic-thinking", "problem-solving"],
      "unit": "Unit 1: Foundations",
      "createdAt": "2025-09-01T00:00:00.000Z"
    }
  ],
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Update Course

Update course information.

```http
PUT /api/courses/{courseId}
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Advanced Foundations of Algebra",
  "totalHours": 75
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "course_001",
    "title": "Advanced Foundations of Algebra",
    "description": "An introductory course covering fundamental algebraic concepts...",
    "subject": "Mathematics",
    "level": "beginner",
    "totalHours": 75,
    "learningObjectives": ["..."],
    "prerequisites": [],
    "createdAt": "2025-09-01T00:00:00.000Z",
    "updatedAt": "2025-11-07T10:00:00.000Z"
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Delete Course

Remove a course from the system.

```http
DELETE /api/courses/{courseId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "message": "Course deleted successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

---

### Content Management

Content items represent individual learning materials such as lessons, videos, quizzes, and assignments.

#### Create Content

Add a new content item to the curriculum.

```http
POST /api/content
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Introduction to Variables",
  "description": "Learn what variables are and how to use them in algebraic expressions",
  "type": "lesson",
  "difficulty": "beginner",
  "estimatedMinutes": 45,
  "courseId": "course_001",
  "order": 1,
  "skills": ["variables", "algebraic-expressions", "problem-solving"],
  "unit": "Unit 1: Foundations"
}
```

**Required Fields:**
- `title` (string): Content title
- `description` (string): Content description
- `type` (string): "lesson", "video", "quiz", or "assignment"
- `difficulty` (string): "beginner", "intermediate", or "advanced"
- `estimatedMinutes` (number): Expected completion time

**Optional Fields:**
- `courseId` (string): Associated course ID
- `order` (number): Position in curriculum sequence
- `skills` (array): List of skills/topics covered
- `unit` (string): Unit or module name

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "content_1730963400000_xyz789",
    "courseId": "course_001",
    "title": "Introduction to Variables",
    "description": "Learn what variables are and how to use them in algebraic expressions",
    "type": "lesson",
    "difficulty": "beginner",
    "estimatedMinutes": 45,
    "order": 1,
    "skills": ["variables", "algebraic-expressions", "problem-solving"],
    "unit": "Unit 1: Foundations",
    "createdAt": "2025-11-07T10:00:00.000Z"
  },
  "message": "Content created successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### List All Content

Retrieve all content items with filtering and sorting.

```http
GET /api/content?search=variable&type=lesson&difficulty=beginner&sortBy=order&order=asc
Authorization: Bearer <token>
```

**Query Parameters:**
- `search` (string): Search by title or description
- `type` (string): Filter by content type
- `difficulty` (string): Filter by difficulty level
- `courseId` (string): Filter by course
- `sortBy` (string): Sort field (title, order, createdAt)
- `order` (string): "asc" or "desc"

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "content_001",
      "courseId": "course_001",
      "title": "Introduction to Variables",
      "description": "Learn what variables are...",
      "type": "lesson",
      "difficulty": "beginner",
      "estimatedMinutes": 45,
      "order": 1,
      "skills": ["variables", "algebraic-expressions"],
      "unit": "Unit 1: Foundations",
      "createdAt": "2025-09-01T00:00:00.000Z"
    }
  ],
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Get Content by ID

Retrieve detailed information about specific content.

```http
GET /api/content/{contentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "content_001",
    "courseId": "course_001",
    "title": "Introduction to Variables",
    "description": "Learn what variables are and how to use them in algebraic expressions",
    "type": "lesson",
    "difficulty": "beginner",
    "estimatedMinutes": 45,
    "order": 1,
    "skills": ["variables", "algebraic-expressions", "problem-solving"],
    "unit": "Unit 1: Foundations",
    "createdAt": "2025-09-01T00:00:00.000Z"
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Update Content

Update content information.

```http
PUT /api/content/{contentId}
Content-Type: application/json
Authorization: Bearer <token>

{
  "title": "Advanced Introduction to Variables",
  "estimatedMinutes": 60,
  "difficulty": "intermediate"
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "content_001",
    "courseId": "course_001",
    "title": "Advanced Introduction to Variables",
    "description": "Learn what variables are and how to use them in algebraic expressions",
    "type": "lesson",
    "difficulty": "intermediate",
    "estimatedMinutes": 60,
    "order": 1,
    "skills": ["variables", "algebraic-expressions", "problem-solving"],
    "unit": "Unit 1: Foundations",
    "createdAt": "2025-09-01T00:00:00.000Z",
    "updatedAt": "2025-11-07T10:00:00.000Z"
  },
  "message": "Content updated successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Delete Content

Remove content from the system.

```http
DELETE /api/content/{contentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "message": "Content deleted successfully",
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

#### Get Content Statistics

Retrieve aggregate statistics about all content.

```http
GET /api/content/stats
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "total": 120,
    "byType": {
      "lesson": 65,
      "video": 30,
      "quiz": 20,
      "assignment": 5
    },
    "byDifficulty": {
      "beginner": 45,
      "intermediate": 50,
      "advanced": 25
    },
    "totalEstimatedHours": 95.5
  },
  "timestamp": "2025-11-07T10:00:00.000Z"
}
```

---

## Data Collection & Logging APIs

These APIs enable you to track student activities, progress, and results in real-time.

### Activity Event Logging

Activity events track all student interactions with content, including views, completions, time spent, and scores.

#### Log Activity Event

Record a student activity event.

```http
POST /api/events
Content-Type: application/json
Authorization: Bearer <token>

{
  "studentId": "student_123",
  "contentId": "content_001",
  "eventType": "complete",
  "timestamp": "2025-11-07T10:30:00.000Z",
  "durationMs": 2820000,
  "completed": true,
  "score": 85,
  "metadata": {
    "browser": "Chrome",
    "platform": "Windows"
  },
  "sessionId": "session_abc123",
  "attemptNumber": 1,
  "struggledFlag": false,
  "pauseCount": 2,
  "idleTimeMs": 120000,
  "deviceType": "desktop"
}
```

**Required Fields:**
- `studentId` (string): Student's unique ID
- `contentId` (string): Content item's unique ID
- `eventType` (string): "view_start", "view_end", or "complete"

**Optional Fields:**
- `timestamp` (string): Event timestamp (defaults to current time)
- `durationMs` (number): Time spent in milliseconds
- `completed` (boolean): Whether content was completed
- `score` (number): Score (0-100) for assessments
- `metadata` (object): Additional custom data
- `sessionId` (string): Group related events
- `attemptNumber` (number): Attempt count for retries
- `struggledFlag` (boolean): Indicates difficulty
- `pauseCount` (number): Number of pauses during session
- `idleTimeMs` (number): Idle time in milliseconds
- `deviceType` (string): "desktop", "mobile", or "tablet"

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "event_1730963400000_xyz789",
    "studentId": "student_123",
    "contentId": "content_001",
    "eventType": "complete",
    "timestamp": "2025-11-07T10:30:00.000Z",
    "durationMs": 2820000,
    "completed": true,
    "score": 85,
    "metadata": {
      "browser": "Chrome",
      "platform": "Windows"
    },
    "sessionId": "session_abc123",
    "attemptNumber": 1,
    "struggledFlag": false,
    "pauseCount": 2,
    "idleTimeMs": 120000,
    "deviceType": "desktop"
  },
  "message": "Event logged successfully",
  "timestamp": "2025-11-07T10:30:00.000Z"
}
```

#### Get Activity Events

Retrieve activity logs with filtering.

```http
GET /api/events?studentId=student_123&contentId=content_001&startDate=2025-11-01&endDate=2025-11-07&eventType=complete&limit=50&offset=0
Authorization: Bearer <token>
```

**Query Parameters:**
- `studentId` (string): Filter by student
- `contentId` (string): Filter by content
- `startDate` (string): Start date (YYYY-MM-DD)
- `endDate` (string): End date (YYYY-MM-DD)
- `eventType` (string): Filter by event type
- `limit` (number): Results per page (default: 50)
- `offset` (number): Skip results (default: 0)

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "event_123",
      "studentId": "student_123",
      "contentId": "content_001",
      "eventType": "complete",
      "timestamp": "2025-11-07T10:30:00.000Z",
      "durationMs": 2820000,
      "completed": true,
      "score": 85,
      "metadata": {},
      "sessionId": "session_abc123",
      "attemptNumber": 1,
      "struggledFlag": false,
      "pauseCount": 2,
      "idleTimeMs": 120000,
      "deviceType": "desktop"
    }
  ],
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

### Course Progress Tracking

Track student progress through courses with completion rates and scores.

#### Get Student Course Progress

Retrieve a student's progress in a specific course.

```http
GET /api/progress/student/{studentId}/course/{courseId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "progress_123",
    "studentId": "student_123",
    "courseId": "course_001",
    "totalContent": 18,
    "completedContent": 12,
    "progressPercentage": 66.67,
    "avgScore": 78.5,
    "startedAt": "2025-09-15T08:00:00.000Z",
    "completedAt": null,
    "contentProgress": {
      "content_001": {
        "completed": true,
        "score": 85,
        "completedAt": "2025-09-15T09:00:00.000Z"
      },
      "content_002": {
        "completed": true,
        "score": 72,
        "completedAt": "2025-09-16T10:00:00.000Z"
      }
    }
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

#### Get All Course Progress for Student

Retrieve all course progress for a specific student.

```http
GET /api/progress/student/{studentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": [
    {
      "id": "progress_123",
      "studentId": "student_123",
      "courseId": "course_001",
      "totalContent": 18,
      "completedContent": 12,
      "progressPercentage": 66.67,
      "avgScore": 78.5,
      "startedAt": "2025-09-15T08:00:00.000Z",
      "completedAt": null
    },
    {
      "id": "progress_124",
      "studentId": "student_123",
      "courseId": "course_002",
      "totalContent": 24,
      "completedContent": 24,
      "progressPercentage": 100,
      "avgScore": 92.3,
      "startedAt": "2025-08-01T08:00:00.000Z",
      "completedAt": "2025-10-20T16:00:00.000Z"
    }
  ],
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

#### Update Course Progress

Update progress information for a student in a course.

```http
PUT /api/progress/student/{studentId}/course/{courseId}
Content-Type: application/json
Authorization: Bearer <token>

{
  "completedContent": 15,
  "progressPercentage": 83.33,
  "avgScore": 82.0,
  "completedAt": null
}
```

**Response:**

```json
{
  "success": true,
  "data": {
    "id": "progress_123",
    "studentId": "student_123",
    "courseId": "course_001",
    "totalContent": 18,
    "completedContent": 15,
    "progressPercentage": 83.33,
    "avgScore": 82.0,
    "startedAt": "2025-09-15T08:00:00.000Z",
    "completedAt": null
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

## Analytics & Intelligence Layer

The analytics layer provides AI-powered insights into student performance, engagement, and learning patterns.

### Dashboard Overview

Get a comprehensive overview of class performance with key metrics, trends, and at-risk students.

```http
GET /api/analytics/overview
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "summary": {
      "totalStudents": 150,
      "totalContent": 120,
      "totalActivities": 8450,
      "avgEngagementRate": 72.5,
      "avgCompletionRate": 68.3,
      "trends": [
        {
          "date": "2025-11-01",
          "activities": 245,
          "completions": 168,
          "uniqueStudents": 132
        },
        {
          "date": "2025-11-02",
          "activities": 268,
          "completions": 182,
          "uniqueStudents": 138
        }
      ]
    },
    "recentActivities": [
      {
        "id": "event_123",
        "studentId": "student_123",
        "studentName": "John Doe",
        "contentId": "content_001",
        "contentTitle": "Introduction to Variables",
        "eventType": "complete",
        "timestamp": "2025-11-07T10:30:00.000Z",
        "score": 85
      }
    ],
    "atRiskStudents": [
      {
        "student": {
          "id": "student_456",
          "name": "Jane Smith",
          "email": "jane.smith@school.edu",
          "grade": "10",
          "class": "10-B"
        },
        "riskLevel": "high",
        "reason": "No activity in the past 7 days and engagement score below 40%",
        "missingSkills": ["linear-equations", "graphing"],
        "passProbability": 35.2
      }
    ],
    "aiInsight": "Overall class performance is strong with 72.5% engagement. However, 12 students show concerning patterns including extended inactivity and low completion rates. Immediate interventions recommended for high-risk students."
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

### Student Analytics

Get detailed analytics for an individual student, including activity logs, strengths, and improvement areas.

```http
GET /api/analytics/students/{studentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "student": {
      "id": "student_123",
      "name": "John Doe",
      "email": "john.doe@school.edu",
      "grade": "10",
      "class": "10-A",
      "enrolledAt": "2025-09-01T00:00:00.000Z",
      "status": "active"
    },
    "analytics": {
      "studentId": "student_123",
      "totalTimeMinutes": 1248,
      "completedContent": 24,
      "avgScore": 82.5,
      "engagementScore": 78.3,
      "riskLevel": "low",
      "activityLog": [
        {
          "contentTitle": "Introduction to Variables",
          "contentType": "lesson",
          "timestamp": "2025-11-07T10:30:00.000Z",
          "completed": true,
          "score": 85,
          "timeMinutes": 47
        },
        {
          "contentTitle": "Solving Linear Equations",
          "contentType": "quiz",
          "timestamp": "2025-11-06T14:15:00.000Z",
          "completed": true,
          "score": 92,
          "timeMinutes": 35
        }
      ],
      "strengths": [
        "Demonstrates strong mastery with an average score of 82.5%",
        "Consistent engagement with 78.3% engagement score",
        "Efficient time management - completes content within estimated time"
      ],
      "improvements": [
        "Could benefit from reviewing graphing concepts",
        "Consider attempting more challenging advanced content"
      ],
      "aiInsight": "John Doe is performing excellently with consistent engagement and strong comprehension across topics. He shows particular strength in problem-solving and algebraic thinking. Consider providing advanced enrichment materials to maintain momentum."
    }
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Engagement Score Calculation:**
```
Engagement Score = (Activity Frequency × 0.4) + (Completion Rate × 0.4) + (Content Coverage × 0.2)

Where:
- Activity Frequency = min((Recent 7-day events / 5) × 100, 100)
- Completion Rate = (Completed events / Total events) × 100
- Content Coverage = (Unique content accessed / Total content) × 100
```

**Risk Level Determination:**
- **High**: No activity in last 7 days OR engagement < 40% OR avg score < 50%
- **Medium**: Engagement < 65% OR avg score < 70%
- **Low**: Otherwise

---

### Content Analytics

Analyze the effectiveness and difficulty of specific content items.

```http
GET /api/analytics/content/{contentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "content": {
      "id": "content_001",
      "title": "Introduction to Variables",
      "type": "lesson",
      "difficulty": "beginner",
      "estimatedMinutes": 45
    },
    "analytics": {
      "contentId": "content_001",
      "totalViews": 142,
      "avgTimeMinutes": 52.3,
      "completionRate": 87.3,
      "avgScore": 78.5,
      "performanceRating": "good"
    },
    "studentPerformance": [
      {
        "studentId": "student_123",
        "studentName": "John Doe",
        "timeMinutes": 47,
        "completed": true,
        "score": 85,
        "timestamp": "2025-11-07T10:30:00.000Z"
      },
      {
        "studentId": "student_456",
        "studentName": "Jane Smith",
        "timeMinutes": 68,
        "completed": true,
        "score": 62,
        "timestamp": "2025-11-06T14:00:00.000Z"
      }
    ],
    "aiInsight": "This lesson shows good overall performance with 87.3% completion rate. However, average time spent (52 minutes) exceeds estimated time (45 minutes), suggesting the content may be slightly more challenging than intended. Consider adding clarifying examples or breaking into smaller segments."
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Performance Rating Logic:**
- **Excellent**: Completion ≥ 80%, Score ≥ 75%, Time ≤ 1.3× estimated
- **Good**: Completion ≥ 60%, Score ≥ 60%
- **Needs Improvement**: Otherwise

---

### Engagement Analytics

Deep dive into engagement patterns including session frequency, time analysis, and drop-off points.

```http
GET /api/analytics/engagement
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "timePerLesson": [
      {
        "contentId": "content_001",
        "title": "Introduction to Variables",
        "avgTimeMinutes": 52.3,
        "estimatedMinutes": 45,
        "studentCount": 142,
        "completionRate": 87.3
      }
    ],
    "sessionFrequency": [
      {
        "studentId": "student_123",
        "studentName": "John Doe",
        "activeDays": 18,
        "totalDays": 30,
        "frequencyPercentage": 60.0
      }
    ],
    "dropOffPoints": [
      {
        "fromContent": "Linear Equations Basics",
        "toContent": "Advanced Linear Systems",
        "dropOffCount": 24,
        "dropOffRate": 16.9,
        "courseId": "course_001"
      }
    ],
    "timeComparison": [
      {
        "contentId": "content_001",
        "title": "Introduction to Variables",
        "estimatedMinutes": 45,
        "actualMinutes": 52.3,
        "ratio": 1.16,
        "status": "too_slow"
      }
    ],
    "streaks": [
      {
        "studentId": "student_123",
        "studentName": "John Doe",
        "currentStreak": 7,
        "longestStreak": 12,
        "lastActiveDate": "2025-11-07"
      }
    ]
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

### Performance Analytics

Identify struggling students, high performers, and analyze lesson difficulty.

```http
GET /api/analytics/performance
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "lessonDifficulty": [
      {
        "contentId": "content_005",
        "title": "Quadratic Equations",
        "difficultyScore": 68.5,
        "rating": "very_hard",
        "metrics": {
          "avgTimeMinutes": 78.2,
          "struggleRate": 0.45,
          "completionRate": 0.62,
          "avgPauses": 8.3,
          "avgScore": 58.2
        }
      }
    ],
    "strugglingStudents": [
      {
        "studentId": "student_789",
        "studentName": "Mike Johnson",
        "struggleScore": 42.5,
        "indicators": {
          "struggleRate": 0.38,
          "completionRate": 0.55,
          "multipleAttempts": 8,
          "avgScore": 62.3
        }
      }
    ],
    "highPerformers": [
      {
        "studentId": "student_123",
        "studentName": "John Doe",
        "performanceScore": 84.2,
        "metrics": {
          "avgScore": 88.5,
          "completionRate": 0.95,
          "timeEfficiency": 0.92,
          "firstTryRate": 0.88
        }
      }
    ],
    "performanceTrends": [
      {
        "week": "Week 1",
        "startDate": "2025-10-28",
        "endDate": "2025-11-03",
        "avgScore": 76.8,
        "completionRate": 82.5,
        "activeStudents": 138
      },
      {
        "week": "Week 2",
        "startDate": "2025-11-04",
        "endDate": "2025-11-10",
        "avgScore": 78.2,
        "completionRate": 85.1,
        "activeStudents": 142
      }
    ]
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Lesson Difficulty Score Formula (0-100):**
```
Difficulty Score = TimeScore + StruggleScore + CompletionScore + PauseScore + ScoreScore

Where:
- TimeScore = min((timeRatio - 1) × 50, 50)
- StruggleScore = struggleRate × 30
- CompletionScore = (1 - completionRate) × 10
- PauseScore = min(avgPauses / 10 × 5, 5)
- ScoreScore = (100 - avgScore) / 10

Ratings:
- Very Hard: ≥ 60
- Hard: 40-59
- Moderate: 20-39
- Easy: < 20
```

---

### Cohort Analytics

Compare performance across classes and identify lagging groups.

```http
GET /api/analytics/cohort
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "slowdownLessons": [
      {
        "contentId": "content_005",
        "title": "Quadratic Equations",
        "estimatedMinutes": 60,
        "actualMinutes": 92.5,
        "slowdownRate": 54.2,
        "affectedStudents": 128,
        "timeRatio": 1.54
      }
    ],
    "bestContent": [
      {
        "contentId": "content_002",
        "title": "Basic Algebra Operations",
        "type": "lesson",
        "performanceScore": 92.5,
        "metrics": {
          "completionRate": 0.96,
          "avgScore": 88.2,
          "engagementCount": 145,
          "timeEfficiency": 0.98
        }
      }
    ],
    "laggingGroups": [
      {
        "grade": "10",
        "class": "10-C",
        "studentCount": 28,
        "avgScore": 58.3,
        "completionRate": 62.5,
        "engagementRate": 55.8,
        "status": "lagging"
      },
      {
        "grade": "10",
        "class": "10-A",
        "studentCount": 30,
        "avgScore": 82.7,
        "completionRate": 88.3,
        "engagementRate": 85.2,
        "status": "on_track"
      }
    ]
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

## Assessment Analytics APIs

Track student mastery of specific topics and skills through detailed assessment data.

### Topic Mastery

Get comprehensive topic mastery analysis across all students.

```http
GET /api/analytics/assessments
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "topicMastery": [
      {
        "topic": "linear-equations",
        "topicName": "Linear Equations",
        "masteryPercentage": 78.5,
        "masteryLevel": "proficient",
        "totalQuestions": 240,
        "correctAnswers": 188,
        "incorrectAnswers": 52,
        "studentsAttempted": 142
      },
      {
        "topic": "quadratic-equations",
        "topicName": "Quadratic Equations",
        "masteryPercentage": 52.3,
        "masteryLevel": "developing",
        "totalQuestions": 180,
        "correctAnswers": 94,
        "incorrectAnswers": 86,
        "studentsAttempted": 135
      }
    ],
    "knowledgeGaps": [
      {
        "topic": "systems-of-equations",
        "topicName": "Systems of Equations",
        "masteryPercentage": 42.8,
        "masteryLevel": "struggling",
        "totalQuestions": 160,
        "correctAnswers": 68,
        "incorrectAnswers": 92,
        "studentsAttempted": 128,
        "strugglingStudentCount": 52,
        "strugglingStudents": [
          {
            "studentId": "student_456",
            "studentName": "Jane Smith",
            "topicScore": 35.0,
            "questionsAttempted": 8,
            "questionsCorrect": 3
          }
        ]
      }
    ],
    "scoreDistribution": {
      "bins": [
        { "range": "0-20", "min": 0, "max": 20, "count": 8 },
        { "range": "21-40", "min": 21, "max": 40, "count": 12 },
        { "range": "41-60", "min": 41, "max": 60, "count": 28 },
        { "range": "61-80", "min": 61, "max": 80, "count": 54 },
        { "range": "81-100", "min": 81, "max": 100, "count": 40 }
      ],
      "statistics": {
        "avgScore": 72.5,
        "medianScore": 75.0,
        "maxScore": 98,
        "minScore": 12,
        "totalAssessments": 142,
        "passingRate": 78.9
      }
    },
    "questionDifficulty": [
      {
        "contentId": "content_quiz_001",
        "contentTitle": "Linear Equations Quiz",
        "questionNumber": 3,
        "topic": "slope-intercept",
        "topicName": "Slope-Intercept Form",
        "totalAttempts": 142,
        "correctAttempts": 58,
        "successRate": 40.8,
        "avgTimeSpent": 145,
        "difficulty": "hard"
      }
    ],
    "performanceMatrix": {
      "matrix": [
        {
          "studentId": "student_123",
          "studentName": "John Doe",
          "topicScores": {
            "linear-equations": 88.5,
            "quadratic-equations": 75.0,
            "systems-of-equations": 82.3,
            "graphing": 91.2
          }
        }
      ],
      "topics": [
        { "topic": "linear-equations", "topicName": "Linear Equations" },
        { "topic": "quadratic-equations", "topicName": "Quadratic Equations" },
        { "topic": "systems-of-equations", "topicName": "Systems of Equations" },
        { "topic": "graphing", "topicName": "Graphing" }
      ]
    },
    "commonMistakes": [
      {
        "topic": "quadratic-equations",
        "topicName": "Quadratic Equations",
        "errorCount": 86,
        "errorRate": 47.7,
        "affectedStudentCount": 68
      }
    ],
    "totalAssessments": 142,
    "totalStudents": 150
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Mastery Levels:**
- **Mastered**: ≥ 80% correct
- **Proficient**: 60-79% correct
- **Developing**: 40-59% correct
- **Struggling**: < 40% correct

---

### Knowledge Gap Analysis

Identify specific topics where students are struggling.

Knowledge gaps are automatically included in the `/api/analytics/assessments` endpoint under the `knowledgeGaps` field, showing topics with mastery percentage below 60% and listing affected students.

---

### Question-Level Analysis

Analyze difficulty and success rates for individual assessment questions.

Question-level data is included in the `/api/analytics/assessments` endpoint under the `questionDifficulty` field.

---

### Performance Matrix

Get a grid view of student performance across all topics.

The performance matrix is included in the `/api/analytics/assessments` endpoint under the `performanceMatrix` field, providing a student × topic grid with individual scores.

---

### Student Assessment Details

Get detailed assessment results for a specific student.

```http
GET /api/analytics/assessments/student/{studentId}
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "studentId": "student_123",
    "studentName": "John Doe",
    "assessments": [
      {
        "eventId": "event_456",
        "contentId": "content_quiz_001",
        "contentTitle": "Linear Equations Quiz",
        "score": 85,
        "completed": true,
        "timestamp": "2025-11-05T14:30:00.000Z",
        "questions": [
          {
            "questionNumber": 1,
            "topic": "linear-equations",
            "topicName": "Linear Equations",
            "isCorrect": true,
            "timeSpent": 120,
            "attempts": 1
          },
          {
            "questionNumber": 2,
            "topic": "slope-intercept",
            "topicName": "Slope-Intercept Form",
            "isCorrect": false,
            "timeSpent": 180,
            "attempts": 2
          }
        ]
      }
    ],
    "topicMastery": [
      {
        "topic": "linear-equations",
        "topicName": "Linear Equations",
        "masteryPercentage": 88.5,
        "questionsAttempted": 26,
        "questionsCorrect": 23
      }
    ],
    "weakAreas": [
      {
        "topic": "systems-of-equations",
        "topicName": "Systems of Equations",
        "masteryPercentage": 52.0,
        "questionsAttempted": 12,
        "questionsCorrect": 6
      }
    ],
    "strongAreas": [
      {
        "topic": "graphing",
        "topicName": "Graphing",
        "masteryPercentage": 91.2,
        "questionsAttempted": 18,
        "questionsCorrect": 16
      }
    ],
    "totalAssessments": 8,
    "avgScore": 82.5
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

## AI-Powered Insights

The AI layer provides automated analysis, narratives, and actionable recommendations based on analytics data.

### Comprehensive Analysis

Generate a complete AI-powered analysis with narratives, root causes, and recommendations.

```http
GET /api/ai/analysis
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "narrative": [
      {
        "type": "positive",
        "category": "engagement",
        "text": "Overall engagement is strong at 72.5% with consistent daily participation across most classes."
      },
      {
        "type": "concern",
        "category": "performance",
        "text": "24 students are showing signs of struggle with completion rates below 60% and multiple failed attempts."
      },
      {
        "type": "neutral",
        "category": "cohort",
        "text": "Class 10-C is lagging behind with 58.3% average score compared to 82.7% for Class 10-A."
      }
    ],
    "rootCauses": [
      {
        "issue": "High drop-off rate after 'Linear Equations Basics'",
        "evidence": "24 students (16.9%) discontinued after this lesson",
        "likelyCauses": [
          "Content difficulty spike - next lesson 'Advanced Linear Systems' may be too challenging",
          "Insufficient foundational understanding before advancing",
          "Lesson pacing or length may be overwhelming"
        ],
        "affectedLessons": ["Advanced Linear Systems", "Systems of Equations"],
        "affectedStudents": ["student_456", "student_789"],
        "affectedClasses": ["10-C"]
      }
    ],
    "recommendations": [
      {
        "priority": "high",
        "category": "intervention",
        "title": "Immediate Support for At-Risk Students",
        "description": "12 students have shown no activity in 7+ days and have engagement scores below 40%",
        "actions": [
          "Schedule one-on-one check-ins with each at-risk student",
          "Provide personalized learning plans focusing on missing skills",
          "Set up peer tutoring or small group sessions",
          "Monitor progress weekly with follow-up interventions"
        ],
        "expectedImpact": "Reduce at-risk student count by 50% within 2 weeks"
      },
      {
        "priority": "high",
        "category": "content",
        "title": "Revise 'Quadratic Equations' Lesson",
        "description": "Content shows difficulty score of 68.5 with 45% struggle rate and 92.5 minutes average completion time (vs 60 estimated)",
        "actions": [
          "Break lesson into smaller, more digestible segments",
          "Add more worked examples and practice problems",
          "Include visual aids and interactive demonstrations",
          "Provide prerequisite review at the beginning"
        ],
        "expectedImpact": "Improve completion rate from 62% to 80% and reduce struggle rate to <30%",
        "affectedContent": ["content_005"]
      },
      {
        "priority": "medium",
        "category": "engagement",
        "title": "Boost Engagement in Class 10-C",
        "description": "Class shows significantly lower engagement (55.8%) and performance (58.3% avg score) compared to other classes",
        "actions": [
          "Investigate class-specific factors (teaching style, schedule, environment)",
          "Implement gamification elements to increase motivation",
          "Provide additional support sessions for the entire class",
          "Consider pairing with high-performing class for collaborative activities"
        ],
        "expectedImpact": "Increase class engagement to 70% and average score to 70% within 4 weeks"
      },
      {
        "priority": "medium",
        "category": "pacing",
        "title": "Address Content Pacing Issues",
        "description": "Several lessons show significant time overruns (30-50% longer than estimated)",
        "actions": [
          "Review and update time estimates based on actual data",
          "Identify if content is too dense or requires prerequisite knowledge",
          "Consider adding 'quick review' sections for struggling students",
          "Optimize content delivery format (text, video, interactive)"
        ],
        "expectedImpact": "Better student time management and reduced frustration"
      },
      {
        "priority": "low",
        "category": "retention",
        "title": "Leverage High Performers as Peer Mentors",
        "description": "15 students demonstrate exceptional performance (avg score >85%, high engagement)",
        "actions": [
          "Create peer mentoring program pairing high performers with struggling students",
          "Recognize and reward top performers to maintain motivation",
          "Provide advanced enrichment content to prevent boredom",
          "Gather feedback from high performers about what works well"
        ],
        "expectedImpact": "Improve overall class cohesion and provide leadership opportunities"
      }
    ],
    "nextSteps": [
      {
        "timeframe": "immediate",
        "step": 1,
        "action": "Contact At-Risk Students",
        "description": "Reach out to 12 high-risk students within 24 hours to understand barriers and offer support",
        "effort": "Medium",
        "impact": "High"
      },
      {
        "timeframe": "immediate",
        "step": 2,
        "action": "Schedule Class 10-C Review Session",
        "description": "Set up additional support session for underperforming class focusing on struggling topics",
        "effort": "Low",
        "impact": "Medium"
      },
      {
        "timeframe": "short-term",
        "step": 3,
        "action": "Revise Quadratic Equations Content",
        "description": "Break down lesson into 3 smaller modules with additional examples and practice",
        "effort": "High",
        "impact": "High"
      },
      {
        "timeframe": "short-term",
        "step": 4,
        "action": "Update Time Estimates",
        "description": "Review all content with >20% time overrun and update estimates based on actual data",
        "effort": "Low",
        "impact": "Low"
      },
      {
        "timeframe": "medium-term",
        "step": 5,
        "action": "Launch Peer Mentoring Program",
        "description": "Pair 15 high performers with struggling students for weekly tutoring sessions",
        "effort": "Medium",
        "impact": "Medium"
      },
      {
        "timeframe": "long-term",
        "step": 6,
        "action": "Implement Gamification",
        "description": "Add badges, leaderboards, and achievement tracking to boost engagement",
        "effort": "High",
        "impact": "Medium"
      }
    ],
    "generatedAt": "2025-11-07T11:00:00.000Z"
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Timeframes:**
- **Immediate**: Within 1-2 days
- **Short-term**: Within 1-2 weeks
- **Medium-term**: Within 1 month
- **Long-term**: 1+ months

---

### Student Notes

Generate AI-powered summary and recommendations for an individual student.

```http
GET /api/ai/student/{studentId}/notes
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "studentId": "student_123",
    "studentName": "John Doe",
    "summary": "John is a high-performing student demonstrating excellent comprehension and consistent engagement. He completes content efficiently and scores well above class average at 82.5%. His learning patterns show strong self-motivation and effective study habits.",
    "strengths": [
      "Excellent average score of 82.5% across all assessments",
      "High engagement score (78.3%) with regular daily participation",
      "Efficient time management - completes lessons within estimated time",
      "Strong mastery of graphing (91.2%) and linear equations (88.5%)",
      "First-try success rate of 88% indicates good initial understanding"
    ],
    "challenges": [
      "Systems of equations topic shows lower mastery at 52%",
      "Some struggle with multi-step word problems requiring more attempts",
      "Occasional longer-than-expected time on complex problem sets"
    ],
    "suggestions": [
      {
        "category": "enrichment",
        "priority": "high",
        "suggestion": "Provide advanced problem sets and challenge questions to maintain engagement and prevent boredom"
      },
      {
        "category": "support",
        "priority": "medium",
        "suggestion": "Offer targeted review on systems of equations with additional practice problems and examples"
      },
      {
        "category": "leadership",
        "priority": "medium",
        "suggestion": "Consider recruiting as peer mentor to help struggling students while reinforcing own understanding"
      },
      {
        "category": "monitoring",
        "priority": "low",
        "suggestion": "Continue monitoring to ensure consistent performance and catch any emerging difficulties early"
      }
    ],
    "metrics": {
      "completedLessons": 24,
      "totalTimeMinutes": 1248,
      "avgScore": 82.5,
      "struggleRate": 0.12,
      "consistencyScore": 88.5,
      "activeDays": 22
    },
    "generatedAt": "2025-11-07T11:00:00.000Z"
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

### Content Notes

Generate AI-powered analysis and improvement suggestions for specific content.

```http
GET /api/ai/content/{contentId}/notes
Authorization: Bearer <token>
```

**Response:**

```json
{
  "success": true,
  "data": {
    "contentId": "content_005",
    "contentTitle": "Quadratic Equations",
    "summary": "This lesson presents significant challenges for students with a high difficulty score of 68.5 and struggle rate of 45%. Students are taking 54% longer than estimated (92.5 vs 60 minutes), and the completion rate of 62% indicates many students are abandoning the content.",
    "difficultyAssessment": {
      "rating": "Very Difficult",
      "description": "This content is significantly more challenging than intended for its difficulty level",
      "score": 68.5,
      "metrics": {
        "timeOverrun": "+54% (92.5 vs 60 min)",
        "struggleRate": "45% of students showed struggle indicators",
        "completionRate": "62% (below target of 80%)",
        "avgScore": "58.2% (below passing threshold)"
      }
    },
    "commonIssues": [
      "Students frequently pause (avg 8.3 pauses) suggesting confusion or overwhelm",
      "High rate of multiple attempts indicates insufficient initial understanding",
      "Low average score (58.2%) shows many students not grasping core concepts",
      "Significant time overrun suggests content is too dense or complex"
    ],
    "improvements": [
      {
        "priority": "high",
        "area": "Content Structure",
        "suggestion": "Break lesson into 3 smaller modules: (1) Introduction & Basics, (2) Solving Methods, (3) Applications & Practice"
      },
      {
        "priority": "high",
        "area": "Instructional Design",
        "suggestion": "Add 5-8 worked examples with step-by-step solutions before practice problems"
      },
      {
        "priority": "high",
        "area": "Prerequisites",
        "suggestion": "Include a 'What You Need to Know' section reviewing factoring and square roots at the beginning"
      },
      {
        "priority": "medium",
        "area": "Visual Aids",
        "suggestion": "Incorporate graphs showing parabolas and visual representations of solutions"
      },
      {
        "priority": "medium",
        "area": "Practice Opportunities",
        "suggestion": "Provide scaffolded practice problems starting with simple examples and gradually increasing complexity"
      },
      {
        "priority": "medium",
        "area": "Formative Assessment",
        "suggestion": "Add checkpoint quizzes after each sub-section to ensure understanding before proceeding"
      },
      {
        "priority": "low",
        "area": "Time Estimate",
        "suggestion": "Update estimated time from 60 to 90 minutes to set realistic expectations"
      }
    ],
    "metrics": {
      "studentCount": 135,
      "completionRate": 62.0,
      "avgTimeMinutes": 92.5,
      "estimatedMinutes": 60,
      "timeRatio": 1.54,
      "avgScore": 58.2,
      "struggleRate": 45.0
    },
    "generatedAt": "2025-11-07T11:00:00.000Z"
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

## Reports & Export

Generate and export comprehensive reports in various formats.

### Export Dashboard Report

```http
POST /api/reports/export
Content-Type: application/json
Authorization: Bearer <token>

{
  "format": "csv",
  "reportType": "dashboard",
  "dateRange": {
    "start": "2025-11-01",
    "end": "2025-11-07"
  },
  "includeCharts": false,
  "includeAiInsights": true,
  "includeMetrics": true,
  "includeDetails": true,
  "filters": {
    "grade": "10",
    "class": "10-A"
  }
}
```

**Request Parameters:**
- `format` (string): "csv" or "pdf"
- `reportType` (string): "dashboard", "student", "content", "assessment"
- `dateRange` (object): Start and end dates
- `includeCharts` (boolean): Include chart data
- `includeAiInsights` (boolean): Include AI-generated insights
- `includeMetrics` (boolean): Include key metrics
- `includeDetails` (boolean): Include detailed data
- `filters` (object): Optional filters

**Response:**

```json
{
  "success": true,
  "data": {
    "reportId": "report_1730963400000_xyz",
    "downloadUrl": "https://api.intellector-analyzer.com/v1/reports/download/report_1730963400000_xyz",
    "expiresAt": "2025-11-08T11:00:00.000Z",
    "format": "csv",
    "fileSize": 245678
  },
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

### Download Report

```http
GET /api/reports/download/{reportId}
Authorization: Bearer <token>
```

Returns the report file as a downloadable attachment.

---

## Data Models

### Student Model

```json
{
  "id": "string (auto-generated)",
  "name": "string (required)",
  "email": "string (required, unique)",
  "grade": "string (required)",
  "class": "string (required)",
  "enrolledAt": "ISO timestamp (auto-generated)",
  "status": "active | inactive (default: active)"
}
```

### Course Model

```json
{
  "id": "string (auto-generated)",
  "title": "string (required)",
  "description": "string (required)",
  "subject": "string (required)",
  "level": "beginner | intermediate | advanced (required)",
  "totalHours": "number (required)",
  "learningObjectives": "string[] (optional)",
  "prerequisites": "string[] (optional)",
  "createdAt": "ISO timestamp (auto-generated)",
  "updatedAt": "ISO timestamp (auto-updated)"
}
```

### Content Model

```json
{
  "id": "string (auto-generated)",
  "courseId": "string (optional)",
  "title": "string (required)",
  "description": "string (required)",
  "type": "lesson | video | quiz | assignment (required)",
  "difficulty": "beginner | intermediate | advanced (required)",
  "estimatedMinutes": "number (required)",
  "order": "number (optional)",
  "skills": "string[] (optional)",
  "unit": "string (optional)",
  "createdAt": "ISO timestamp (auto-generated)",
  "updatedAt": "ISO timestamp (auto-updated)"
}
```

### Activity Event Model

```json
{
  "id": "string (auto-generated)",
  "studentId": "string (required, foreign key)",
  "contentId": "string (required, foreign key)",
  "eventType": "view_start | view_end | complete (required)",
  "timestamp": "ISO timestamp (default: current time)",
  "durationMs": "number (optional)",
  "completed": "boolean (optional)",
  "score": "number 0-100 (optional)",
  "metadata": "object (optional)",
  "sessionId": "string (optional)",
  "attemptNumber": "number (optional)",
  "struggledFlag": "boolean (optional)",
  "pauseCount": "number (optional)",
  "idleTimeMs": "number (optional)",
  "deviceType": "desktop | mobile | tablet (optional)"
}
```

### Course Progress Model

```json
{
  "id": "string (auto-generated)",
  "studentId": "string (required, foreign key)",
  "courseId": "string (required, foreign key)",
  "totalContent": "number (calculated)",
  "completedContent": "number (calculated)",
  "progressPercentage": "number 0-100 (calculated)",
  "avgScore": "number (calculated)",
  "startedAt": "ISO timestamp (auto-generated)",
  "completedAt": "ISO timestamp | null",
  "contentProgress": "object (optional)"
}
```

---

## Error Handling

### Error Response Format

```json
{
  "success": false,
  "message": "Detailed error message",
  "code": "ERROR_CODE",
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

### Common Error Codes

| Code | HTTP Status | Description |
|------|-------------|-------------|
| `INVALID_REQUEST` | 400 | Missing required fields or invalid data format |
| `VALIDATION_ERROR` | 400 | Data validation failed |
| `UNAUTHORIZED` | 401 | Missing or invalid authentication token |
| `FORBIDDEN` | 403 | Insufficient permissions for requested action |
| `NOT_FOUND` | 404 | Requested resource does not exist |
| `DUPLICATE_ENTRY` | 409 | Resource already exists (e.g., duplicate email) |
| `INTERNAL_ERROR` | 500 | Server-side error occurred |

### Example Error Responses

**Missing Required Field:**

```json
{
  "success": false,
  "message": "Missing required field: email",
  "code": "VALIDATION_ERROR",
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Duplicate Email:**

```json
{
  "success": false,
  "message": "A student with this email already exists",
  "code": "DUPLICATE_ENTRY",
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

**Resource Not Found:**

```json
{
  "success": false,
  "message": "Student with ID 'student_999' not found",
  "code": "NOT_FOUND",
  "timestamp": "2025-11-07T11:00:00.000Z"
}
```

---

## Calculation Methods

### Engagement Score (0-100)

```
Engagement Score = (Activity Frequency × 0.4) + (Completion Rate × 0.4) + (Content Coverage × 0.2)

Where:
- Activity Frequency = min((Recent 7-day events / 5) × 100, 100)
- Completion Rate = (Completed events / Total events) × 100
- Content Coverage = (Unique content accessed / Total content) × 100
```

### Risk Level Determination

```
IF no activity in last 7 days → HIGH
ELSE IF engagement < 40% OR avgScore < 50% → HIGH
ELSE IF engagement < 65% OR avgScore < 70% → MEDIUM
ELSE → LOW
```

### Pass Probability (0-100)

```
Pass Probability = (Engagement Score × 0.5) + (Average Score × 0.5)
```

### Content Performance Rating

```
IF completion ≥ 80% AND avgScore ≥ 75% AND avgTime ≤ 1.3×estimated → EXCELLENT
ELSE IF completion ≥ 60% AND avgScore ≥ 60% → GOOD
ELSE → NEEDS_IMPROVEMENT
```

### Lesson Difficulty Score (0-100)

```
Difficulty Score = TimeScore + StruggleScore + CompletionScore + PauseScore + ScoreScore

Where:
- TimeScore = min((timeRatio - 1) × 50, 50)
- StruggleScore = struggleRate × 30
- CompletionScore = (1 - completionRate) × 10
- PauseScore = min(avgPauses / 10 × 5, 5)
- ScoreScore = (100 - avgScore) / 10

Ratings:
- Very Hard: ≥ 60
- Hard: 40-59
- Moderate: 20-39
- Easy: < 20
```

### Student Performance Score (0-100)

For identifying high performers:

```
Performance Score = (avgScore/100 × 40) + (completionRate × 25) +
                   ((1 - struggleRate) × 15) + (firstTryRate × 10) +
                   (max(0, (1 - avgTimeRatio)) × 10)

High Performer: ≥ 70
```

### Student Struggle Score (0-100)

For identifying struggling students:

```
Struggle Score = (struggleRate × 40) + ((1 - completionRate) × 30) +
                (min(multipleAttempts/totalEvents, 0.5) × 20) +
                ((100 - avgScore)/100 × 10)

Struggling: ≥ 30
```

### Topic Mastery Percentage

```
Mastery Percentage = (Correct Answers / Total Questions) × 100

Mastery Levels:
- Mastered: ≥ 80%
- Proficient: 60-79%
- Developing: 40-59%
- Struggling: < 40%
```

---

## Best Practices

### 1. Use Unique IDs Consistently

Always use the system-generated IDs (e.g., `student_123`, `content_001`) when referencing resources. Never assume ID formats or create your own IDs.

### 2. Log Events in Real-Time

For accurate analytics, log activity events as they occur. Batch logging can lead to inaccurate time tracking and engagement metrics.

### 3. Include Metadata

Use the `metadata` field in activity events to capture additional context (browser, device, IP, etc.) that may be useful for analysis.

### 4. Set Realistic Time Estimates

When creating content, base estimated time on pilot testing. The system will flag content with significant time overruns for review.

### 5. Monitor At-Risk Students

Check the `/api/analytics/overview` endpoint daily to identify and intervene with at-risk students early.

### 6. Use Filters Effectively

Leverage query parameters to filter large datasets and reduce API response times.

### 7. Implement Pagination

Always use pagination for list endpoints to avoid large responses and timeouts.

### 8. Handle Errors Gracefully

Check the `success` field in responses and handle errors appropriately with user-friendly messages.

### 9. Cache Responses When Appropriate

Cache analytics data that doesn't change frequently (e.g., course curriculum) to reduce API calls.

### 10. Validate Data Before Submission

Validate all required fields client-side before making API requests to reduce failed requests.

---

## Rate Limits

- **Standard Tier**: 1000 requests/hour per API key
- **Premium Tier**: 5000 requests/hour per API key
- **Enterprise Tier**: Custom limits

Rate limit headers are included in all responses:

```
X-RateLimit-Limit: 1000
X-RateLimit-Remaining: 987
X-RateLimit-Reset: 2025-11-07T12:00:00.000Z
```

---

## Support

For API support, technical questions, or feature requests:

- **Email**: api-support@intellector-analyzer.com
- **Documentation**: https://docs.intellector-analyzer.com
- **Status Page**: https://status.intellector-analyzer.com
- **API Changelog**: https://docs.intellector-analyzer.com/changelog

---

## Changelog

### Version 1.0.0 (November 2025)
- Initial API release
- Complete CRUD operations for students, courses, and content
- Activity event logging with comprehensive tracking
- Multi-level analytics (dashboard, student, content, engagement, performance, cohort)
- Assessment analytics with topic mastery tracking
- AI-powered insights and recommendations
- CSV/PDF export functionality

---

**© 2025 Intellector Analyzer. All rights reserved.**