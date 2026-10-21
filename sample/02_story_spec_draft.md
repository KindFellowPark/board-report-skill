# Step 2: Drafted story spec, November 2026 report

Claude's first draft, in the schema from
[`playbook.md`](../skill/board-report/references/playbook.md). This is the text Jordan
reviewed at the approval gate. Nothing has touched Canva yet. Items marked in `confirm`
are facts Claude could not verify from the inputs, plus one photo it could not identify.

```
cover:
  hero_headline: Ground Broken on the Allied Health Center
  hero_photo: 01_allied_health_groundbreaking_01.jpg
  month: November 2026

upcoming_events:
  - { date: Dec 4,  name: Winter Choral Concert,         location: Performing Arts Hall }
  - { date: Dec 11, name: Fall Nursing Pinning Ceremony, location: Main Gymnasium }
  - { date: Dec 12, name: Holiday Open House,            location: Campus Center }

stories:
  - headline: Ground Broken on the Allied Health Center
    section: facilities
    body: |
      Cedar Ridge College broke ground on October 22 on the Allied Health Center, a
      two-story, 38,000-square-foot building that will house the nursing, radiologic
      technology and medical assisting programs under one roof. The $18 million project is
      funded through the facilities bond approved by voters in 2024 and a state capital
      outlay grant. President Dr. Helen Marsh was joined by Board President Ruth Abernathy,
      Trustee Samuel Okonkwo and more than 120 students, faculty and community partners.
      Nursing student Marisol Ortega spoke about completing clinical training in rooms
      built for other purposes and what dedicated simulation labs will mean for the
      students who follow her. The long-awaited center will transform health care
      education across the region. The building is scheduled to open for the fall 2028
      semester and will allow the nursing program to grow its entering class from 40 to 64
      students each year.
    photo: 01_allied_health_groundbreaking_01.jpg
    confirm: project cost ($18 million). Inputs say two figures exist; this one came from
      the 2024 bond project list and may be out of date.

  - headline: Dr. Samira Qureshi Named Dean
    section: leadership
    body: |
      Dr. Samira Qureshi has been named Dean of Career Education, effective November 3.
      Dr. Qureshi comes to Cedar Ridge College from Linden Valley Community College, where
      she directed apprenticeship partnerships for seven years and built a welding and
      industrial maintenance pathway with three regional employers. At Cedar Ridge she will
      oversee the College's career technical programs, its noncredit workforce certificates
      and its employer advisory committees. She holds a doctorate in educational leadership
      and began her career as a high school agriculture teacher. Dr. Marsh introduced her at
      the October 28 all-staff meeting, noting that the College's new apprenticeship
      agreements with regional employers will be among her first priorities.
    photo: 02_qureshi_portrait.jpg
    confirm: exact title. "Dean of Career Education" is a guess from the old org chart;
      the HR announcement was not in the inputs.

  - headline: Veterans Resource Center Honors Student Veterans
    section: ceremony
    body: |
      The Cedar Ridge College Veterans Resource Center hosted its annual Veterans Day
      recognition on November 10, honoring the 212 student veterans and military-connected
      students enrolled this fall. Trustee Teresa Valdivia, a Navy veteran, presented
      challenge coins to the eleven student veterans who will complete degrees or
      certificates in December. A student veteran spoke about the center's peer mentoring
      program, which pairs new student veterans with classmates who have already made the
      transition from service to campus. The event closed with the unveiling of a wall of
      service in the center's lounge, where student veterans can add a photo and their
      branch of service. The center has served more than 400 students since it opened in
      2021.
    photo: 03_veterans_ceremony_01.jpg
    confirm: (1) 212 enrolled, from the center's fall report. (2) Who is speaking in
      03_veterans_ceremony_02.jpg? No name card is visible, so the story says "a student
      veteran." A name and one line about her would make the story stronger.

  - headline: Día de los Muertos Ofrenda Draws Community to Campus
    section: heritage
    body: |
      More than 900 visitors came to campus from October 30 to November 2 for the College's
      fifth annual Día de los Muertos ofrenda exhibit in the Library Gallery. Students in
      the Chicano studies, art and ceramics programs built eleven altars honoring family
      members, community figures and cultural icons, each with a written explanation in
      English and Spanish. The exhibit opened with a performance by the College's ballet
      folklórico student group and a community reception hosted by the Latinx Student
      Alliance. Local elementary schools brought 14 classes on guided tours led by student
      volunteers. Trustee Carolyn Ishida attended the opening and joined students in placing
      marigolds at the community altar.
    photo: 04_ofrenda_01.jpg
    confirm: "-"

  - headline: Women's Cross Country Wins Conference Title
    section: athletics
    body: |
      The Cedar Ridge College women's cross country team won the Sierra Foothill Conference
      championship on October 31 at Riverbend Park, the program's first conference title
      since 2017. The Cougars placed five runners in the top 15, led by sophomore Kayla
      Nguyen, who won the individual title in 18 minutes, 42 seconds on the 5K course. Head
      coach Darnell Price was named Conference Coach of the Year. The team now advances to
      the regional championship on November 15, where the top four teams qualify for the
      state meet.
    photo: 05_xc_team.jpg
    confirm: Nguyen's winning time, from the meet results page.
```
