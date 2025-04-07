# 🧑‍💻 Freelancer Management & Job Portal

This platform will be a centralized system for managing a community of freelancers, allowing admins to post job requirements, and enabling freelancers to register, get verified, and apply for jobs in a streamlined, trackable manner.

---

## 🚀 Key Features and Workflow

### ✅ 1. User Roles
- **Admin**
- **Freelancer**

---

### 👤 2. Freelancer Registration & Onboarding Flow

- **Landing Page with CTA (Join as Freelancer)**  
  Shared via social media, email, or referral link.

- **Social Login / Email Signup**  
  OAuth with Google, LinkedIn, or GitHub.

- **Freelancer Onboarding Form**  
  Captures the following details:
  - Personal Info (Name, Email, Phone, Location)
  - Skills (Multi-select)
  - Years of Experience
  - Resume Upload
  - Portfolio Links (LinkedIn, GitHub, Dribbble, etc.)
  - Preferred Work Type (Part-time, Contract, Full-time)
  - Availability & Hourly Rate

- **Verification Workflow**
  - Goes into a pending state for Admin approval.
  - Admin reviews profile/resume and approves or rejects.
  - Email notification sent to freelancer on approval or rejection.

---

### 🧑‍💼 3. Freelancer Dashboard (Post Approval)

- View/Edit Profile  
- View Job Listings  
- Apply to Jobs (with CV + Cover Letter)  
- Track Application Status  
- Receive Notifications (job posted, application update, messages)  

---

### 📂 4. Admin Panel

#### 📋 Freelancer Management
- View all registered freelancers
- Filter by: Skills, Years of Experience, Availability, Location
- Approve/Reject pending freelancers
- Download CVs / Export freelancer list
- View complete freelancer profiles

#### 📝 Job Management
- Post New Job Requirements:
  - Title
  - Description
  - Required Skills
  - Duration
  - Pay Range
  - Deadline

- Bulk Email Notification to all approved freelancers (via SendGrid/Mailgun)
- View and manage applications (sorted per job)
- Download submitted CVs
- Update application status (e.g., shortlisting, interview, closed)

---

### 📬 5. Job Notification & Application Flow

- When Admin posts a job:
  - All relevant freelancers are notified via email
  - Job appears on freelancer dashboard
  - Freelancer can apply with:
    - Custom Cover Letter (textarea)
    - Updated CV upload

---

### 🔎 6. Advanced Filtering & Search (Admin)

Admins can search and filter freelancers based on:
- Tags / Skills
- Minimum Experience
- Availability
- Location / Timezone

---

## SubDomains 

- talent.cloudprism.in
- freelance.cloudprism.in
- work.cloudprism.in
- hub.cloudprism.in
- gigs.cloudprism.in


Absolutely! Here's the **database schema** structured in **tabular format** for better visibility, followed by the **API endpoints** in table form too.

---

## 📦 Database Design (Tables)

### 1. `users`
| Field            | Type                          | Description                          |
|------------------|-------------------------------|--------------------------------------|
| `id`             | UUID (PK)                     | Primary key                          |
| `role`           | ENUM: 'admin', 'freelancer'   | User role                            |
| `email`          | VARCHAR                       | Email address                        |
| `password_hash`  | TEXT (nullable)               | Hashed password                      |
| `oauth_provider` | ENUM: 'google', 'github'...   | OAuth provider (optional)            |
| `oauth_id`       | VARCHAR (nullable)            | OAuth unique ID                      |
| `created_at`     | TIMESTAMP                     | When created                         |
| `updated_at`     | TIMESTAMP                     | When last updated                    |

---

### 2. `freelancer_profiles`
| Field               | Type                            | Description                                 |
|---------------------|----------------------------------|---------------------------------------------|
| `id`                | UUID (PK)                       | Primary key                                 |
| `user_id`           | UUID (FK → users.id)            | Link to user                                |
| `name`              | VARCHAR                         | Full name                                   |
| `phone`             | VARCHAR                         | Contact number                              |
| `location`          | VARCHAR                         | City, Country                               |
| `skills`            | TEXT[] / JSON                   | List of skills                              |
| `experience_years`  | INTEGER                         | Years of experience                         |
| `resume_url`        | TEXT                            | Link to uploaded resume                     |
| `portfolio_links`   | JSON (linkedin, github, etc.)   | External links                              |
| `preferred_work_type` | ENUM: 'part_time' ...         | Preferred type of work                      |
| `availability`      | BOOLEAN                         | Currently available?                        |
| `hourly_rate`       | DECIMAL                         | Expected rate                               |
| `status`            | ENUM: 'pending', 'approved'...  | Verification status                         |
| `admin_remarks`     | TEXT                            | Admin feedback                              |
| `created_at`        | TIMESTAMP                       | When created                                |
| `updated_at`        | TIMESTAMP                       | When updated                                |

---

### 3. `jobs`
| Field         | Type             | Description                           |
|---------------|------------------|---------------------------------------|
| `id`          | UUID (PK)        | Job ID                                |
| `title`       | VARCHAR          | Job title                             |
| `description` | TEXT             | Job description                       |
| `required_skills` | TEXT[] / JSON | Skill tags                            |
| `duration`    | VARCHAR          | Duration of the job                   |
| `pay_min`     | DECIMAL          | Minimum pay                           |
| `pay_max`     | DECIMAL          | Maximum pay                           |
| `deadline`    | DATE             | Application deadline                  |
| `created_by`  | UUID (FK → users.id) | Posted by admin                     |
| `created_at`  | TIMESTAMP        | When created                          |
| `updated_at`  | TIMESTAMP        | When updated                          |

---

### 4. `job_applications`
| Field          | Type                              | Description                        |
|----------------|-----------------------------------|------------------------------------|
| `id`           | UUID (PK)                         | Primary key                        |
| `freelancer_id`| UUID (FK → users.id)              | Freelancer who applied             |
| `job_id`       | UUID (FK → jobs.id)               | Job applied for                    |
| `cover_letter` | TEXT                              | Custom cover letter                |
| `cv_url`       | TEXT                              | Uploaded CV                        |
| `status`       | ENUM: 'applied', 'interview'...   | Application status                 |
| `created_at`   | TIMESTAMP                         | When applied                       |
| `updated_at`   | TIMESTAMP                         | Last update                        |

---

### 5. `notifications`
| Field       | Type                             | Description                          |
|-------------|----------------------------------|--------------------------------------|
| `id`        | UUID (PK)                        | Primary key                          |
| `user_id`   | UUID (FK → users.id)             | Recipient user                       |
| `message`   | TEXT                             | Notification content                 |
| `is_read`   | BOOLEAN                          | Has the user read it?                |
| `type`      | ENUM: 'job_posted', 'message'... | Notification type                    |
| `created_at`| TIMESTAMP                        | Time of creation                     |

---

## 🌐 API Endpoints Overview

### 🔐 Auth
| Method | Endpoint                       | Description                     |
|--------|--------------------------------|---------------------------------|
| POST   | `/api/auth/signup`            | Register (freelancer)           |
| POST   | `/api/auth/login`             | Login                           |
| GET    | `/api/auth/oauth/callback`    | OAuth callback                  |
| POST   | `/api/auth/logout`            | Logout                          |

---

### 👤 Freelancer
| Method | Endpoint                             | Description                          |
|--------|--------------------------------------|--------------------------------------|
| GET    | `/api/freelancer/profile`            | Get profile                          |
| PUT    | `/api/freelancer/profile`            | Update profile                       |
| POST   | `/api/freelancer/resume`             | Upload resume                        |
| GET    | `/api/freelancer/applications`       | List job applications                |
| POST   | `/api/freelancer/apply/:jobId`       | Apply to a job                       |

---

### 🧑‍💼 Admin: Freelancer Management
| Method | Endpoint                                  | Description                           |
|--------|-------------------------------------------|---------------------------------------|
| GET    | `/api/admin/freelancers`                 | List freelancers (filters supported) |
| GET    | `/api/admin/freelancer/:id`              | View freelancer profile               |
| POST   | `/api/admin/freelancer/:id/approve`      | Approve freelancer                    |
| POST   | `/api/admin/freelancer/:id/reject`       | Reject freelancer                     |
| GET    | `/api/admin/freelancers/export`          | Export list/download CVs             |

---

### 📝 Admin: Job Management
| Method | Endpoint                                 | Description                          |
|--------|------------------------------------------|--------------------------------------|
| GET    | `/api/admin/jobs`                        | List jobs                            |
| POST   | `/api/admin/jobs`                        | Post a new job                       |
| PUT    | `/api/admin/jobs/:id`                    | Update job                           |
| DELETE | `/api/admin/jobs/:id`                    | Delete job                           |
| GET    | `/api/admin/jobs/:id/applications`       | View job applicants                  |
| POST   | `/api/admin/applications/:id/status`     | Update application status            |

---

### 📬 Notifications
| Method | Endpoint                             | Description                        |
|--------|--------------------------------------|------------------------------------|
| GET    | `/api/notifications`                 | List user notifications            |
| POST   | `/api/notifications/:id/read`        | Mark notification as read          |

