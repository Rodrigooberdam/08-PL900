---
lab:
    title: 'Lab 02: Canvas app, Part 2'
    module: 'Power Apps Build'
---

# PL-900: Microsoft-Power-Platform-Fundamentals
## Lab 2 – Canvas App - Part 2

Scenario
========

Bellows College is an educational organization with multiple buildings on campus. Campus visits are currently recorded in paper journals. The information is not captured consistently and there are no means to collect and analyze data about the visits across the entire campus. 

Campus administration would like to modernize their visitor registration system where access to the buildings is controlled by security personnel and all visits are required to be pre-registered and recorded by their hosts.

Throughout this course you will build applications and perform automation to enable the Bellows College administration and security personnel to manage and control access to the buildings on campus. 

In part 2 of this lab, you will create design and build a Power Apps canvas app that the security personnel will use at the building entrances to quickly confirm and register the visitors.

High-level lab steps
======================

You will follow the below outline to design the canvas app:

-   Create the app using the phone form factor
-   Connect to Common Data Service as a data source
-   Capture the input (visitor code) and locate the visitor record
-   Configure a form viewer control to show the visitor information
-   Use a CDS view to populate the gallery
-   Handle checking-in and checking-out process for a visitor


## Prerequisites

* Completion of Lab 1 - Data Modeling

Things to consider before you begin
-----------------------------------

-   What information would a security officer need quick access to?
-   What should happen if visitor code is invalid
-   What should happen if the visitor arrives outside of the scheduled hours 

Exercise \#1: Create Security Canvas App
===============================

**Objective:** In this exercise, you will create a canvas app.

Task \#1: Create Canvas App
---------------------------

1.  Open the Campus Management solution.

    -   Sign in to <https://make.powerapps.com>

    -   Select your **environment.**

    -   Select **Solutions**.

    -   Click to open the **Campus Management** solution.
2.  Create new canvas application

    -   Click **New** and select **App &#124; Canvas App &#124; Phone Form Factor**.
        This will open the App Editor in a New window.
-   If you are creating your first app, this will ask you to set the
        Country/region for the app. Click **Get Started.**
-   Click File and select Save As.
-   Check if **The Cloud** is selected. Enter **Campus Security** for Name and
        click **Save**. This will make sure that the changes are not removed if
    the app closes unexpectedly.
3.  Connect to data source (Visits)
    1.  Click **View &#124; Data sources**
    2.  Click **See all entities**
    3.  Select **Visit**
4.  To preserve work in progress, click **File &#124; Save** then press **Save**

Task \#2: Display Visitor information
--------------------------------

1.  Add search box

    -   Select the **Tree View** tab
    -   Select the **Screen1**.
    -   Go to the **Insert** tab.
    -   Click **Text** and select **Text input**.
    -   Select **Default** property and clear the value.
    -   Select **HintText** property and enter **"Enter visitor code"** as a value (including double quotes)
    -   Click on ... next to control name in a tree view, select **Rename**, change the name to **textCode**
2. Add form view

   -   On **Insert** tab click **Forms**  then select **Display**
   -   Using size handles position the form below the search textbox
   -   Select **DataSource** property and enter **Visits**
   -   In properties pane select **Horizontal** as **Layout**
   -   Click **Edit fields**
   -   Click **Add field** and select the following fields: Actual End, Actual Start, Building, Scheduled End, Scheduled Start, Visitor
   -   Press **Add**
   -   Change the order of the selected fields by dragging the field cards in the list. Recommended order is Visitor, Building, Scheduled Start, Scheduled End, Actual Start, Actual End
   -   Select **Item** property and enter `LookUp(Visit, Code = textCode.Text)` 

3.  To preserve work in progress, click **File &#124; Save** then press **Save**

4. Test the app

   -   Switch to the browser tab containing the solution
   -   Select **Visit** entity
   -   Select **Data** tab
   -   Click **Select view** then select **All fields**
   -   Select and copy any of the values displayed in the **Code** column
   -   Switch to the browser tab with the app, press F5 to run the app
   -   Paste the copied value into the search textbox, verify that the record is displayed in the form
5.  Press **ESC** to exit the running app.


Task \#3: Add Check In and Check Out Buttons
---------------------------------------

1. Save search results in a variable to reuse across the control

   * Select **textCode** control
   * Select **OnChange** property
   * Enter the following expression `Set(CurrentVisit, LookUp(Visit, Code = textCode.Text))`
     It is the same expression as above except this time we save results in a global variable. That allows us to use the variable *CurrentVisit* throughout the app without the need to re-enter the entire lookup expression.

2. Add buttons for checking in and checking out
   
   * Select **Insert** tab
   * Click **Button**
   * Change the button **Text** property to **"Check In"**
   * Rename the button as **CheckInButton**
   * Click **Button** to insert another button
   * Change the button **Text** property to **"Check Out"**
   * Rename the button as **CheckOutButton**
   * Position the buttons side by side below the record form view 
   
3. Enable and disable buttons depending on visit data. 
   We would like to enable **Check In** button when the visit record has been located (not blank), record status is active, and the visit has not started yet, i.e. the actual start value is blank.

   * Select the **DisplayMode** property of the button

   * Enter the expression below.

      ```
      If(!IsBlank(CurrentVisit) 
      && IsBlank(CurrentVisit.'Actual Start'),
          DisplayMode.Edit,
          DisplayMode.Disabled
      )
      ```

   The expression can be broken down as following:

   * `!IsBlank(CurrentVisit)` - visit record was found
   * `&&` - logical AND operator
   * `IsBlank(CurrentVisit.'Actual Start')` - Active Start field does not have any data in it

4.  We would like to enable **Check Out** button when the visit record has been located (not blank), record status is active, and the visit has already started, i.e. the actual start value is not blank.

   * Select the **DisplayMode** property of the **Check Out** button

   * Enter the expression below.

     ```
     If(!IsBlank(CurrentVisit) 
     && !IsBlank(CurrentVisit.'Actual Start'),
         DisplayMode.Edit,
         DisplayMode.Disabled
     )
     ```

11. To preserve work in progress, click **File &#124; Save** then press **Save**.

12. Press **F5** to run the app. Both buttons should be disabled. Enter the code value you copied previously and press **Tab** to move the focus away from the textbox. The **Check In** button should become enabled.

13. Press **ESC** to exit the running app.

## Task #4: Complete Check In and Check Out Process

To perform the check in and check out process we need to update CDS visit data as following:

* When visitor checks in, set *Actual Start* field to the current date and time
* When visitor checks out, set *Actual End* field to the current date and time. 
* After check out, set the record status to inactive indicating that the visit has been completed

1. Select **Check In** button.

2. Set **OnSelect** property  to the following expression.

   ```
   Patch(
       Visit,
       CurrentVisit,
       {'Actual Start': Now()}
   );
   Refresh([@Visit]);
   Set(CurrentVisit, LookUp(Visit, Code = textCode.Text));
   ```

   This expression contains the following parts:

   * `Patch(Visit, CurrentVisit, {'Actual Start': Now()});`. *Patch* method updates **Visit** entity, the record identified by **CurrentVisit** variable (which is the current visit). The expression sets the value of *Actual Start* field to the current date and time (*Now()* method).
   * `Refresh([@Visit]);`. This expression refreshes the visit records as the underlying values have changed
   * `Set(CurrentVisit, LookUp(Visit, Code = textCode.Text));` This expression updates the *CurrentVisit* variable with fresh data from CDS.

3. Select **Check Out** button.

4. Set **OnSelect** property  to the following expression.

   ```
   Patch(
       [@Visit],
       CurrentVisit,
       {
           'Actual End': Now(),
           Status: 'Status (Visits)'.Inactive
       }
   );
   Refresh([@Visit]);
   Set(CurrentVisit, LookUp(Visit, Code = textCode.Text));
   ```

   The only difference from check in expression is setting of the *Status* field to *Inactive* value.

5. To preserve work in progress, click **File &#124; Save** then press **Save**.

6. Press **F5** to run the app. Enter the code value you copied previously and press **Tab** to move the focus away from the textbox. The **Check In** button should become enabled.

7. Press **Check In** button. The following should happen:

   1. *Actual Start* has a current date and time
   2. **Check In** button is disabled
   3. **Check Out** button is enabled

8. Press **Check Out** button.

   1. *Actual End* has a current date and time
   2. Both buttons are disabled

9. Press **ESC** to exit the running app.

Task \#5: Add visual indicators
--------------------------------------

Usability of a mobile app significantly improves when, in addition to the text information, the visual indicators are provided. In this task we will add an icon indicating if a visitor can be checked in or checked out.

1. Select **Inset** tab

2. Select **Icon &#124; Add**. At this point it does not matter which icon we select as we want the value to be dynamic.

3. Resize and place the icon in the middle of the screen below the buttons

4. Select **Icon** property and enter the following expression

   ```
   If(
      CheckInButton.DisplayMode = DisplayMode.Disabled 
   && CheckOutButton.DisplayMode = DisplayMode.Disabled,
       Icon.EmojiFrown,
       Icon.EmojiSmile
   )
   ```

5. To preserve work in progress, click **File &#124; Save** then press **Save**.
6. Press **F5** to run the app. Enter the code value you copied previously and press **Tab** to move the focus away from the textbox. Verify the icon displays a frown emoji.
7. Find a different code value that has not been used before. You can run **Campus Staff** app created previously to create new visit records. Verify the icon displays a smile emoji.
8. Press **ESC** to exit the running app.

## Task #6: Publish the app

1. Select **Campus Security** app, click **Edit**
2. Select **File &#124; Publish** 

# Challenges

* How to avoid manual entry of the visit code?
* Add building validation for the visit
* Add validation of the visit actual time vs visit scheduled time (too early, too late, etc)
* Add detailed status of the visit, e.g. email display and validation for the visitor, reason for denying building access, etc
* How would you handle the requirement of multiple buildings/meetings/checkings during the single campus visit. For example, someone may visit campus for a day and during that day they will meet staff members in multiple buildings at different time of the day. Would you consider bringing *appointment* entity into the soluton?