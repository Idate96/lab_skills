# Student Onboarding Runbook

Canonical skill:

- `/home/lorenzo/git/codex_skills/skills/student-onboarding`
- Main instructions: `/home/lorenzo/git/codex_skills/skills/student-onboarding/SKILL.md`
- Reference details: `/home/lorenzo/git/codex_skills/skills/student-onboarding/references/rsl-student-onboarding.md`

Living document for initializing a new student project in the `RSL_OngoingStudentProjects` shared Drive.

## Scope

When a new student starts, handle these steps:

1. Add the student to the shared spreadsheet.
2. Create the Drive project folder from the template.
3. Add the grading sheet at the outer project-folder level.
4. Request cluster permission.
5. Request compute-room access.
6. Send Fang Nan the student name and GitHub handle so the student can be added to the student GitHub group.

## Shared Resources

- Shared Drive name: `RSL_OngoingStudentProjects`
- Shared Drive id: `0AIU36ZEZMCB3Uk9PVA`
- Project spreadsheet: `List of Student Projects`
- Spreadsheet id: `1w_IP91LTrw-JgUT1bXRjrK6xoZ87yLGG4lZd0Q4qfS0`
- Main tab for active students: `Ongoing Projects`
- `2026` folder id: `151pUcb0tKCMsAw4B8d0A392Oznhqu_Sm`
- `Templates` folder id: `1bPe5jjMBciHGETsrlILS7tBlXJJSAWUX`
- Template content folder `shared`: `16Kf_-zAQUyT_SlSrI0-e80i76QvtL3nL`
- Grading-sheet template: `RSL - Student Evaluation Template`
- Grading-sheet template id: `1RbxUwJWIYBjc8yOdmber_5ASUsQGAP3qaFqfbyjAdUU`
- Kick-off doc template: `Kick-Off-Student-Projects`
- Kick-off doc template id: `1oe128vMqzn_0WGKuLXZMKgeYQ22Xj550BGSYgCXJ-fo`
- PowerPoint template file: `RSL_Template_Powerpoint.pptx`
- PowerPoint template id: `1nev1IQnBXv2dY51997_CFdFnKaNQ5U7a`

## Step 1: Add Student To Spreadsheet

Use the `Ongoing Projects` tab.

Observed layout:

- rows `1-2`: blank
- row `3`: header
- row `4+`: active projects

Important rule:

- New students must be inserted from the top, directly below the header.
- Do not append at the bottom.
- Do not use the helper append flow for this sheet. It appends to the detected table end and can land in the wrong place.

Current safe procedure:

1. Insert a new row at row `4`.
2. Write the student record into `A4:V4`.
3. Verify the row appears directly under the header and before the existing active projects.

Current columns in use:

- `A`: Supervisors
- `B`: Student name
- `C`: Committee Member(s)
- `D`: Github Account
- `E`: Bitbucket Email
- `F`: Thesis Type
- `G`: Title
- `H`: Description
- `I`: Year
- `J`: Start date
- `K`: End date
- `L`: GDrive Folder
- `M`: Completed
- `N`: Report available
- `O`: Grading available
- `P`: Source files available
- `Q`: Access revoked
- `R`: Project Type (HW|SW)
- `S`: currently blank in the sheet layout
- `T`: Grade eDoz (Mariat)
- `U`: Archive Box Nr.
- `V`: Agreed RSL Policy

## Step 2: Create Drive Project Folder

Target location:

- Put the new project under the year folder, currently `2026`.

Observed structure for existing projects:

1. Outer project folder in `/2026`
2. Grading sheet copied into the outer project folder
3. One nested student-material folder inside the outer folder
4. Template documents and numbered subfolders live inside that nested student-material folder

Observed inner template contents come from the `Templates/shared` folder plus a few top-level template files.

Recommended naming:

- Outer folder: use the convention requested by the user for new folders, e.g. `MT - Student Name` or `MA - Student Name`
- Inner template folder: use `<outer folder name> Student Folder`

Template payload to copy into the inner student folder:

- `00_Administration/`
- `01_Meetings/`
- `02_Project_Management/`
- `03_Research/`
- `04_Requirements_and_Function_structure/`
- `05_Concepts/`
- `06_Evaluation/`
- `07_Design/`
- `08_Tests_and_Validation/`
- `09_Report_and_Presentation/`
- `Start_reading_here.docx`
- `Kick-Off-Student-Projects`
- `euler-cluster-guide.txt`
- `lab_organization_student_projects_student_pcs - RSL Wiki.pdf`
- `RSL_Template_Powerpoint.pptx`

Notes:

- `Kick-Off-Student-Projects` exists as a Google Doc template in the template folder.
- The grading sheet belongs in the outer project folder, not inside the nested student-material folder.

## Step 3: Add Grading Sheet

1. Copy `RSL - Student Evaluation Template`.
2. Place the copy in the outer project folder.
3. Keep it outside the nested student-material folder.

Open question:

- Decide whether to keep the default name `RSL - Student Evaluation Template` or rename it to include the student name. Existing folders are inconsistent, so confirm the preferred convention before standardizing.

## Step 4: Request Cluster Permission

Current request form:

- `https://docs.google.com/forms/d/e/1FAIpQLSd9OgeyS7iaZ6WkASOH7kNEfHMGrXIbYdBlzmoWOmLITQJmKw/viewform`

Form title:

- `Request Cluster (Euler) Access`

Required fields:

- `eth_username`
- `last_name`
- `first_name`
- `eth_email`
- `termination_date`
- `supervisors`

Optional field:

- `main_supervisor_email`

Current content expectations:

- `eth_username`: ETH username without domain
- `eth_email`: must match `@ethz.ch`
- `termination_date`: end of thesis / access end date
- `supervisors`: comma-separated names
- `main_supervisor_email`: email of the main supervisor who confirms the request

Notes:

- Tell the student to inform the supervisor first; the form text says the supervisor needs to confirm the application.
- The official references linked by the form are:
  - `https://leggedrobotics.github.io/euler-cluster-guide/`
  - `https://scicomp.ethz.ch/wiki/Euler`

## Step 5: Request Compute-Room Access

TODO:

- identify the exact process and contact for student compute-room access
- record expected lead time and any badge / building prerequisites

## Step 6: Notify Fang Nan For GitHub Group Access

First confirm:

- student full name
- exact GitHub username

Send Fang Nan:

- student full name
- GitHub username

Purpose:

- add the student to the student GitHub group

Preferred contact channel:

- Google Chat API

Current reproducible flow:

1. Make sure `gws` auth includes Chat + Directory scopes.
2. Resolve Fang Nan in the Workspace directory.
3. Resolve the DM space from `users/fannan@leggedrobotics.com`.
4. Send the request with `gws chat spaces messages create`.

Observed working identity and DM:

- email: `fannan@leggedrobotics.com`
- people resource: `people/105618277422297757832`
- DM space: `spaces/l5Wmn4AAAAE`

Worked test message:

- `Hi Fang, could you please add the test student to the student GitHub group? Name: Test Student. GitHub: test-student. This is just a test request while I am validating the onboarding workflow. Thanks.`

## Known Gotchas

- Spreadsheet rows must be inserted at the top; appending is wrong for this sheet.
- The outer project folder and inner student-material folder are different levels.
- The grading sheet must sit in the outer project folder.
- Existing historical folder naming is not fully consistent, so prefer the explicitly requested naming convention for new folders unless told otherwise.
- Before contacting Fang Nan, ask the student for the exact GitHub handle.
