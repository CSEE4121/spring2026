---
# Feel free to add content and custom Front Matter to this file.
# To modify the layout, see https://jekyllrb.com/docs/themes/#overriding-theme-defaults

layout: default
title: Home
menu: main
---

CSEE4121 - Computer Systems for Data Science 
{: style="color:black; font-size: 190%; font-weight:700; text-align: center; padding-top: 15px;"}
Spring '26, Columbia University
{: style="color:black; font-size: 130%; text-align: center; padding-bottom: 30px;"}

----

## Course Overview
Data scientists and engineers increasingly have access to a powerful and broad
range of systems they use to conduct big data analysis and machine learning at
scale: from databases, large-scale analytics to distributed machine learning
frameworks. \\
\\
The goal of this class is to provide data scientists and engineers that work
with big data a better understanding of the foundations of how the systems they
will be using are built. It will also give them a better understanding of the
real-world performance, availability and scalability challenges when using and
deploying these systems at scale. In the course we will cover foundational ideas
in designing these systems, while focusing on specific popular systems that
students are likely to encounter at work or when doing research. The class will
include two written homework and two programming assignments. All of the assignments will be done
individually. In this course we will answer the following questions:
<ul>
  <li>How are popular big data systems designed and architected?</li>
  <li>How to think about performance, scale and reliability of big data systems?</li>
  <li>How do they remain available and not lose data despite frequent server and
hardware failures?</li>
  <li>How to reason about issues like security, privacy and data quality when
conducting analysis on large data sets?</li>
</ul>
{: .text-justify}

The class will be split into two sections, which will have identical content, and will be delivered by the same lecturer (Asaf Cidon) and served by the same TA team. The class will also be recorded, and there is no requirement for physical attendance.


## Instructor
[Waqar Aqeel](https://waqaraqeel.github.io)

## TAs

<ul>
  <li>Krishen Jagani (Head TA): kcj2128@columbia.edu</li>
  <li>Anouksha Rajesh: ar4829@columbia.edu</li>
  <li>Anisha Gupta: ag5213@columbia.edu</li>
  <li>Vaishnavi Kannan: vk2580@columbia.edu</li>
  <li>Sushmita Korishetty: sk5670@columbia.edu</li>
  <li>Arya Shidore: as7870@columbia.edu</li>
</ul>
{: .text-justify}

See [course calendar](https://calendar.google.com/calendar/u/0?cid=Y184ZmU2NjExYjdmMmViMTEwZjQ0OGQwNDVmNmUxMDVhM2M0YjI0ZWI0NDM2NWNkNDVhZDBjYzBjNmIyZmVlNTA3QGdyb3VwLmNhbGVuZGFyLmdvb2dsZS5jb20) for office hour schedule.

## Ed

[Link](https://edstem.org/us/courses/94225/discussion)

## Prerequisites

Students are expected to have solid programming experience in Python. This class is intended to be accessible for
data scientists who do not necessarily have a background in databases, operating
systems or distributed systems.

## Time
Wednesdays 04:10 PM – 06:40 PM

## Exams
Midterm: March 4th, 2026, 4:10 PM - 6:40 PM <br />
Final: April 29th, 2026, 4:10 PM - 6:40 PM

## Grade Breakdown
5% Programming Homework 1 (SQL) <br />
5% Written Homework 1 <br />
10% Programming Homework 2 (indexing and filtering data structures) <br />
5% Written Homework 2 <br />
25% In Person Midterm <br />
50% In Person Final

## Strict Late Submission Policy
There will be no late submissions. Late submissions will receive a grade of 0.
You will have plenty of time to submit your assignments, so exercise proper
time management.

## Collaboration/Copying Policy
All assignments will be done individually. We will enforce this policy when
checking the assignments (we use a code similarity system).

## Course Materials
No textbook. Slides and assignments to be uploaded here.

## Schedule (this is a work in progress, and is likely to change)
[Course Calendar](https://calendar.google.com/calendar/u/0?cid=Y184ZmU2NjExYjdmMmViMTEwZjQ0OGQwNDVmNmUxMDVhM2M0YjI0ZWI0NDM2NWNkNDVhZDBjYzBjNmIyZmVlNTA3QGdyb3VwLmNhbGVuZGFyLmdvb2dsZS5jb20)
<table>
<colgroup>
<col width="33%" />
<col width="45%" />
<col width="22%" />
</colgroup>
<thead>
<tr class="header">
<th>Week</th>
<th>Topic</th>
<th>Homework</th>
</tr>
</thead>
<tbody>
<tr>
<td markdown="span">1</td>
<td markdown="span">Introduction ([Slides]({{ site.baseurl }}{%link slides/lecture-1.pdf %}))</td>
<th></th>
</tr>
<tr>
<td markdown="span">2</td>
<td markdown="span">Infrastructure for Big Data</td>
<th></th>
</tr>
<tr>
<td markdown="span">3</td>
<td markdown="span">Relational Data Model</td>
<th markdown="1">Homework 1 out</th>
</tr>
<tr>
<td markdown="span">4</td>
<td markdown="span">Transactions and Logging</td>
<th></th>
</tr>
<tr>
<td markdown="span">5</td>
<td markdown="span">Storage/memory hierarchy</td>
<th>Homework 2 out</th>
</tr>
<tr>
<td markdown="span">6</td>
<td markdown="span"> Indexing</td>
<th>Homework 1 due</th>
</tr>
<tr>
<td markdown="span">7</td>
<td markdown="span">Midterm</td>
<th>Homework 2 due</th>
</tr>
<tr>
<td markdown="span">8</td>
<td markdown="span">Challenges in Scaling</td>
<th></th>
</tr>
<tr>
<td markdown="span">9</td>
<td markdown="span">Spring Break</td>
<th></th>
</tr>
<tr>
<td markdown="span">10</td>
<td markdown="span">Analytics</td>
<th markdown="1">Homework 3 out</th>
</tr>
<tr>
<td markdown="span">11</td>
<td markdown="span">ML Single Node</td>
<th> Homework 4 out</th>
</tr>
<tr>
<td markdown="span">12</td>
<td markdown="span">Distributed ML</td>
<th></th>
</tr>
<tr>
<td markdown="span">13</td>
<td markdown="span">Security and Privacy</td>
<th></th>
</tr>
<tr>
<td markdown="span">14</td>
<td markdown="span">Guest Lecture</td>
<th> Homework 3 due</th>
</tr>
<tr>
<td markdown="span">15</td>
<td markdown="span">Final Exam</td>
<th>Homework 4 due</th>
</tr>
<tr>


