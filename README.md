# UniSchedule

### _Timetabling without the headache._

A complete, self-contained university course and room scheduling system that runs entirely in your browser — no installation, no server, no account required. Open the file, build your timetable, download the result.

---

## ✨ Features

### Data Management
- Full **create, edit, and delete** support for all scheduling entities
- **Departments** — name and code
- **Rooms** — building, capacity, room type, and features
- **Instructors** — department, email, max teaching load, and per-hour unavailability grid
- **Courses** — code, title, department, credit hours, and expected enrollment
- **Sections** — linked to courses with student count, required room type, and assigned instructor
- **Student Groups** — groups of students with their required courses (used for conflict detection)
- **Time Slot Patterns** — fully configurable (MWF 50-min, TTh 75-min, MW evening, custom)
- **CSV export** for every entity table for easy editing in Excel or Google Sheets
- **Single JSON file** save/load — one file holds your entire schedule

### Schedule Builder
- Visual **weekly grid** (Mon–Fri × 8am–6pm)
- **Drag-and-drop** sections from the unassigned pool onto any time slot
- **Click any chip** to view details, reassign, or lock the assignment in place
- **Real-time conflict detection** with color-coded highlighting:
  - 🔴 Hard conflicts — room double-booked, instructor double-booked, room over capacity, student group overlaps
  - 🟡 Soft conflicts — instructor availability preference violations, room type mismatches
- **Filter** the grid by department, room, or instructor
- **Undo / Redo** — full history with Ctrl+Z / Ctrl+Y

### Auto Solver
- One-click **automated assignment** of all unassigned sections
- Runs in a **Web Worker** so the UI stays responsive while solving
- **Greedy placement** (largest sections first) + **local search** to improve room fit
- Respects all hard constraints:
  - No room double-booking
  - No instructor double-booking
  - Room capacity must not be exceeded
  - Room type must match section requirements
  - Instructor unavailability is avoided
- Optimizes for soft constraints during local search
- **Lock any assignment** before solving to keep it fixed
- Displays a post-solve summary: sections placed, failures, remaining conflicts

### Reports
| Report | Description |
|---|---|
| Room Utilization | % of time slots filled per room, with over-use warnings |
| Instructor Load | Hours per week per instructor, flags overloaded instructors |
| Unassigned Sections | All sections without a room/time assignment |
| Conflicts | Full list of hard and soft constraint violations |

---

## 🚀 Quick Start

### Option 1 — Use the hosted version
Visit the live GitHub Pages URL and click **Load Sample Data** to explore immediately.

### Option 2 — Run locally
1. Download `index.html`
2. Open it in any modern browser (Chrome, Firefox, or Edge)
3. Click **Load Sample Data** or **Open JSON File**

No build step. No dependencies to install. No internet connection required after the first load.

---

## 💾 Data Format

All data is stored in a single JSON file with the following structure:

```json
{
  "meta": {
    "version": "1.0",
    "institution": "State University",
    "term": "Fall 2025"
  },
  "departments": [
    { "id": "abc123", "name": "Computer Science", "code": "CS" }
  ],
  "rooms": [
    {
      "id": "def456",
      "name": "Turing 101",
      "building": "Turing Hall",
      "capacity": 40,
      "roomType": "lecture_hall",
      "features": "projector,whiteboard"
    }
  ],
  "instructors": [
    {
      "id": "ghi789",
      "name": "Dr. Alice Chen",
      "deptId": "abc123",
      "email": "achen@uni.edu",
      "maxLoad": 12,
      "unavailability": { "Mon-8": true, "Fri-16": true }
    }
  ],
  "courses": [
    {
      "id": "jkl012",
      "code": "CS101",
      "title": "Intro to Programming",
      "deptId": "abc123",
      "credits": 3,
      "enrollment": 35
    }
  ],
  "sections": [
    {
      "id": "mno345",
      "courseId": "jkl012",
      "sectionNum": "1",
      "students": 35,
      "roomType": "lab",
      "instructorId": "ghi789"
    }
  ],
  "timeSlotPatterns": [
    {
      "id": "pqr678",
      "name": "MWF 9am",
      "days": "MWF",
      "startTime": "09:00",
      "endTime": "09:50",
      "duration": 50
    }
  ],
  "studentGroups": [
    {
      "id": "stu901",
      "name": "CS Freshmen",
      "size": 35,
      "courseIds": ["jkl012"]
    }
  ],
  "constraints": [],
  "assignments": [
    {
      "sectionId": "mno345",
      "roomId": "def456",
      "timeSlotId": "pqr678",
      "locked": false
    }
  ]
}
```

### Time Slot Day Patterns
The `days` field in a time slot pattern uses the following shorthands:

| Value | Days |
|---|---|
| `MWF` | Monday, Wednesday, Friday |
| `TTh` | Tuesday, Thursday |
| `MW` | Monday, Wednesday |
| `TF` | Tuesday, Friday |
| `MTWRF` | All weekdays |

### Room Types
Valid values for `roomType` on both rooms and sections:

`lecture_hall` · `lab` · `seminar` · `studio` · `auditorium` · `other`

Use `any` on a section to allow assignment to any room type.

---

## ⌨️ Keyboard Shortcuts

| Shortcut | Action |
|---|---|
| `Ctrl + Z` | Undo last action |
| `Ctrl + Y` | Redo |
| `Ctrl + S` | Download JSON |
| `Escape` | Close modal |

---

## 🔧 Technical Details

- **Single HTML file** — all HTML, CSS, and JavaScript are inline; no build step
- **No backend** — everything runs client-side in the browser
- **No frameworks** — plain JavaScript (ES2020+), no React, no Vue
- **Web Worker** — the auto solver runs off the main thread to keep the UI responsive
- **File API** — uses `<input type="file">` for uploads and `URL.createObjectURL` for downloads
- **Fonts** — loaded from Google Fonts CDN (Playfair Display, JetBrains Mono, Source Sans 3); works offline after first load
- **Target browsers** — Chrome, Firefox, and Edge (latest versions)

---

## 📋 Workflow Guide

### Building a schedule from scratch

1. **Settings** — Set your institution name and term
2. **Departments** — Add your departments (e.g. CS, MATH, ENG)
3. **Rooms** — Add all rooms with accurate capacities and types
4. **Instructors** — Add instructors, set their max load, and mark unavailable hours
5. **Courses** — Add course listings with expected enrollment
6. **Sections** — Create sections linked to courses; assign instructors and set student counts
7. **Student Groups** — (Optional) Group students by cohort and link their required courses
8. **Time Slots** — Review or add time slot patterns for your institution's standard meeting times
9. **Auto Solver** — Run the solver to automatically assign all sections to rooms and times
10. **Schedule Builder** — Review the result, drag and drop to adjust, lock preferred assignments
11. **Reports** — Check utilization and resolve any remaining conflicts
12. **Save JSON** — Download your schedule file to preserve the session

### Loading an existing schedule

Click **Open JSON File** on the welcome screen (or **Settings → Load JSON**) and select your previously saved `.json` file. All entities and assignments will be restored exactly as saved.
