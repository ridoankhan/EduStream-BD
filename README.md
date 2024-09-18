# EduStream-BD

a comprehensive online learning platform that allows instructors to create and manage courses, upload video lectures, and quizzes. Students can enroll in courses, watch lessons, take quizzes, and track their learning progress.

# Endpoints

## 1. User Management (Authentication & Authorization)

POST /auth/register: User registration (students and instructors).
POST /auth/login: User login, returning JWT tokens.
GET /auth/profile: Fetch logged-in user profile.
PATCH /auth/profile: Update user profile details (optional).
Roles:

Role-based guards (RolesGuard) to distinguish between students and instructors.

## 2. Course Management (Instructors Only)

POST /courses: Create a new course.
Fields: title, description, category, price.
GET /courses/instructor/
: Get all courses created by a specific instructor.
PATCH /courses/
: Update course details (instructor only).
DELETE /courses/
: Delete a course (instructor only).

## 3. Content Management (Instructors Only)

POST /courses/
/modules: Create a module/section within a course.
Fields: title, description, order.
POST /courses/
/modules/
/videos: Upload and attach video lectures to a module.
Handle file uploads using Multer and store videos in AWS S3.
PATCH /courses/
/modules/
: Update a module (title, description).
DELETE /courses/
/modules/
: Delete a module.

## 4. Course Enrollment (Students)

POST /courses/
/enroll: Enroll in a course (students only).
Add logic to verify payment status (if the course has a price).
GET /courses/
/status: Get the enrollment status of a student (whether enrolled or not).

## 5. Course Content Access (Students)

GET /courses/
: Get course details (available to all users).
GET /courses/
/modules: List all modules within a course (available to enrolled students).
GET /courses/
/modules/
/video: Stream video content (AWS S3 integration).
Video Streaming:

AWS SDK has been to stream videos directly from S3 using signed URLs, or implement custom streaming logic.

## 6. Quiz Management (Instructors)

POST /courses/
/quizzes: Create a quiz for a course.
Fields: title, questions (list of questions and possible answers).
PATCH /courses/
/quizzes/
: Update a quiz.
DELETE /courses/
/quizzes/
: Delete a quiz.

## 7. Quiz Participation (Students)

GET /courses/
/quizzes/
: Get quiz details (students only).
POST /courses/
/quizzes/
/submit: Submit quiz answers and calculate scores.
Logic for scoring and feedback.

## 8. Notifications (Email/Scheduling)

POST /courses/
/notifications: Schedule notifications (e.g., email notifications for upcoming assignments).
Use NestJS Cron or Bull Queue to handle scheduling and background processing for sending reminders or updates to students.
Step 3: Database Models
User:

Fields: id, name, email, role (student/instructor), password.
Relationships: One-to-many with Course, many-to-many with Course for enrollments.
Course:

Fields: id, title, description, category, price, instructorId.
Relationships: One-to-many with Module, many-to-many with User for enrollments.
Module:

Fields: id, title, description, order, courseId.
Relationships: One-to-many with Video.
Video:

Fields: id, url, title, moduleId.
Store video files in S3 and keep metadata (URL, title) in the database.
Quiz:

Fields: id, title, questions (stored as JSON), courseId.
Relationships: One-to-many with QuizSubmission.
QuizSubmission:

Fields: id, studentId, quizId, answers (JSON), score.
