---
lab:
    title: 'Lab 02: Canvas app, Part 1'
    module: 'Power Apps Build'
---

# PL-900: Microsoft-Power-Platform-Fundamentals
## Lab 2 – Canvas App - Part 1

Scenario
========

Bellows College is an educational organization with multiple buildings on campus. Campus visits are currently recorded in paper journals. The information is not captured consistently and there are no means to collect and analyze data about the visits across the entire campus. 

Campus administration would like to modernize their visitor registration system where access to the buildings is controlled by security personnel and all visits are required to be pre-registered and recorded by their hosts.

Throughout this course you will build applications and perform automation to enable the Bellows College administration and security personnel to manage and control access to the buildings on campus.  

In part 1 this lab, you will design a PowerApps canvas app that college staff can use to manage visits for their guests.

High-level lab steps
======================

We will follow the below outline to design the canvas app:

-   Create the app from data using the phone form factor template
-   A detail page with visit info
-   An edit page to create to visits
-   A gallery control to show the visits
-   Add filtering on the gallery data source to show only future visits

## Prerequisites

* Completion of Lab 1 - Data Modeling

Things to consider before you begin
-----------------------------------

-   What is the most prevalent form factor for the target audience
-   Estimate the number of records in the system 
-   How to narrow the records selected to improve app performance and user adoption


Exercise \#1: Create Staff Canvas App
===============================

**Objective:** In this exercise, you will create a canvas app from a template and then modify it to include required data

Task \#1: Create Canvas App
---------------------------

In this task you will create a canvas app using the phone layout template based on Common Data Service. Using Visits as a selected entity from CDS, the template will generate Gallery - View - Edit app to manage campus visits.

1.  Open the Campus Management solution.

    -   Sign in to <https://make.powerapps.com>

    -   Select your **environment.**

    -   Select **Apps**.

2.  Create new canvas application

    -   Click **New app &#124; Canvas app**.
    -   Select **Phone layout** under **Common Data Service** 
-   Select **Common Data Service** connection then click **Create**
    -   Select **Visits** table
    -   Click **Connect**
3.  Save application
    1.  Click **File > Save**
    2.  Type the name of the app as **Campus Staff**
    3.  Press **Save**

Task \#2: Configure Visits Detail Form
--------------------------------

In this task you will configure detail form to view information about individual visit record

1.  Expand **DetailScreen1** under **Tree view**
2.  Select **DetailForm1**
3.  Select **Edit** next to **Fields** in the properties panel
4.  Click **Add field**
5.  Select the following fields
    * Actual End
    * Actual Start
    * Building 
    * Code
    * Scheduled End
    * Scheduled Start
    * Visitor
6.  Click **Add**
7.  Rearrange fields by dragging and dropping field names up or down. Recommended order is:
    * Code, Name, Building, Visitor, Scheduled Start, Scheduled End, Actual Start, Actual End
8.  To preserve work in progress, click **File &#124; Save** then press **Save** 

## Task #3: Configure Visits Edit Form 

In this task you will configure a form to edit information about individual visit record

1.  Expand **EditScreen1** under **Tree view**
2.  Select **EditForm1**
3.  Select **Created On** field and press **Del** key to remove the field
4.  Select **Edit fields** in the properties panel
5.  Click **Add field**
6.  Select the following fields
    * Building 
    * Scheduled End
    * Scheduled Start
    * Visitor
7.  Click **Add**
8.  Rearrange fields by dragging and dropping field names up or down. Recommended order is:
    * Name, Building, Visitor, Scheduled Start, Scheduled End
9.  To preserve work in progress, click **File &#124; Save** then press **Save** 

Task \#4: Configure Visits gallery
---------------------------------------

In this task you will configure pre-generated gallery to display the title, start and end dates for the visit. 

1.  Expand **BrowseScreen1** under **Tree view**
2.  Select **BrowseGallery1**
3.  Select **TemplateSize** property from the property dropdown
4.  Replace the expression with the following `Min(150, BrowseGallery1.Height - 60)`. That will ensure sufficient space for additional information.
5.  Edit the gallery by pressing the pencil icon in the top left corner of the gallery
6.  Select the field with date time 
7.  Change **Text** property from `ThisItem.'Created On'`to `ThisItem.'Scheduled Start'`
8.  Select the field again
9.  Press `CTRL-C` then `CTRL-V` to create a copy of the field.
10.  Using either mouse or keyboard, move the copied control down and align it with the other controls in the gallery
11.  Change **Text** property to`ThisItem.'Scheduled End'`
12.  To preserve work in progress, click **File &#124; Save** then press **Save** 

## Task #6: Add date filter

Because number of visits continuously grows, users need a feature to filter the visits gallery. For example, user may want to see only the future visits. In this task you will add ability to show visits only after a date selected by the user.

1. Select **Insert** menu

2. Click **Input &#124; Date picker** 

3. Using either keyboard or mouse, position control below search box.

4. Select **BrowseGallery1** 

5. Resize and move the gallery control so that it's located under the date picker and covers the screen.

6. Select **Items** property

7. In the expression, locate `[@Visit]` and replace them with `Filter(Visit,'Scheduled End' >= DatePicker1.SelectedDate)`. Full expression should look like the following:

   ```
   SortByColumns(
   	Search(
        Filter(
        	Visit,
            'Scheduled End' >= DatePicker1.SelectedDate
           ),
           TextSearchBox1.Text,
       	"bc_code","bc_name"
       ),
     "bc_code",
     If(SortDescending1, Descending, Ascending)
   )
   ```
   
8. To preserve work in progress, click **File &#124; Save** then press **Save**

# Exercise #2: Complete the App

In this exercise you will test the application and, once successful, you will add it to your solution.

Task \#1: Test App
--------------------------

1.  Start the application

    -   Select the **BrowseScreen1** and press **F5** or click **Preview the App**.
    -   The application should load and show a list of visits. 
    -   Test the filter by selecting different dates in the date picker control
    -   Select a visit and verify that display form is working properly
    -   Return to the gallery and press + to create a new visit. Verify that edit form contains required fields including visitor, building, and scheduled start and end dates.
    -   Fill in the information and submit. Verify that the new record appears in the gallery.
    -   Press **ESC** key to close the app

2.  Save and publish the application

    -   Click **File** and then click **Save**.

    -   Click Publish.

    -   Click **Publish this Version**.

    -   Click **Close**.

    -   Close the **Designer** browser window or tab.

    -   Click **Leave** if prompted when tried to close the browser window.

    -   Navigate back to the previous window and Click **Done**.

## Task #2: Add App to Solution and publish 

1. Open the Campus Management solution.
   * Sign in to <https://make.powerapps.com>
   * Select your **environment.**
   * Select **Solutions**.
   * Click to open the **Campus Management** solution.
2. Select **Add existing &#124; App &#124; Canvas app**
3. Select **Outside solutions** tab
4. Select **Campus Staff** app, click **Add**
5. Select **Campus Staff** app, click **Edit**
6. Select **File &#124; Publish** 

# Challenges

* Calendar view of all visits and filtering by date range
* Ability to create and manage contacts as part of the app
* How to display multiple meetings during a single visit

