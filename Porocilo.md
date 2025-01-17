# Cover Page
# Process Automation PLC-9 Project report
Adam Kern, Peter Savron

FRI – Faculty of Computer Science and Informatics

Mentor: Assist. Prof. dr. Octavian Mihai Machidon

Process Automation - 2024/2025

17/1/2025



## Abstract 

Provide a brief summary (150–200 words) of the project, including objectives, key features, and outcomes.

## Table of Contents

List all sections and subsections with their corresponding page numbers.
Introduction

## 1 Overview of the project
A very well known company, which we cannot name due to signing an NDA, gave us the task of automating their storage facilties by the use of a robotic arm. We were motivated by the promise of a large payout as the project succeeded.

The objective was to create an efficient and user friendly system suitable to their old and untrained workforce for moving large cylindrical containers from the checkout area into the storage.

The development had three phases:
* The replacement of manual labor with manually controlled machine labor.
* Automating the machine labor and removing the costly and accident-prone manual workforce from the task.
* Creating an user friendly UI for a supervisor to supervise the work of the robotic arm and allow for limited flexibility in its automation.

### 1.1 Description of the Task
As some items are put in the checkout area the appropriate recipe is selected by the supervisor and the machine starts transferring the items to their designated location within the storage facility.
Some requirements had to be met:
* Seamlessly transfer an item from point A to B
* Automate the transfer of items
* Map the storage positions to the controller program
* Track the items position inside the storage facility
* Display the data through an intuitive UI
* Allow for changes in automation to be applied through said UI
Specifications:
* The memory of the controller should allow for tracking of up to 5 items and memorisation of 11 positions in the format: 
```
Position: {
    Rotation: INT;
    Exstension: INT;
    Height: INT;
}
```
* The recipe should have up to 9 stages in the format: 
```
Stage: {
    Item-to-take: INT | NULL;
    Position-to-go: INT | NULL;
    Grab: BOOL;
}
```

### 1.2 Overview of the System
The robot has four movable components:
* A rotating base which rotates up to 270 degrees powered by an electrical motor, with a switch at the start point;
* A screw mechanism moves the upper part of the arm vertically, powered by an electric motor with a switch at the top;
* Another screw which extends and retracts the peripheral part of the robotic arm with a switch at the start point;
* A claw which opens and closes with an circular internal profile with a switch which detects the open position.
* A PLC which controls and synchronises the movement of the motors.
### 1.3 Process Description
We simulated the movement of the robotic arm with a small scale replica which we had in our laboratory which we connected to our PLC.
We used Visual Studio as our IDE with the Twincat code environment and build tools installed.
## 2 Control System Design and Implementation
## 2.1 Control Hardware
The robotic arm has eight output variables:
* Four switches with 0/1 values that signal whether the moving part has reached the initial position.
* Four switches which alternate on and off states as the screw rotates emitting an clock like signal with each upper front of the signalling a quarter of the rotation of the screw.

We can give the robotic arm eight inputs:
* We have four boolean inputs which determine the direction of movement of the motor with true being towards the switch and false being away from it;
* We have four inputs which signal the robotic arm which motor to actuate.
The PLC has six buttons, four lights and a switch.

### 2.2 Control Software
The project was composed of two elements, the project for programming the PLC and a project for programming the HMI interface;
Detail the programming environment and techniques.
Explain how manual and automatic modes were implemented.
### 2.3 SCADA Integration (if applicable):
As the SCADA interface is what tbe supervisor will be interacting with the most, we made sure it contains all the relevant information he might need. It contains four gauges for tracking the current position of the robotic arm, designed in an intuitive and user-friendly way. There is a linear horizontal gauge for extension, a linear vertical gauge for height, and radial gauges for rotation and grab. All of the gauges fill up as they go away from the initial switch, which is why the vertical gauge fills up from top to bottom, as the initial switch is located at the top. The supervisor can also monitor the positions of items, as well as the current stage of the recipe, displayed as a linear gauge, allowing for complete overview of the automation process. The items are shown in a table that displays their "ID" as well as their "Rotation", "Extension" and "Height" values which are relative to the position of the robotic arm.
Besides monitoring automatic system processes, the SCADA interface also allows for changing recipes and allowing the facility supervisor to select which recipe he wants to execute. The system is capable of storing 3 completely customizable recipes consisting of up to 9 stages. Each recipe is depicted as a table of 9 stages, with editable values of "Item", "Target" and "Grab". "Item" refers to the "ID" of the item that you want to move, "Target" is the "Position" where the item is supposed to move and "Grab" determines if the item should grabbed or let go of. "Position" is defined in a separate editable table of 11 positions with values of "Rotation", "Extension" and "Height". After making changes to the recipe and position tables the supervisor must press the "apply" button found below each table to commit the changes.
Once the recipes are correctly defined, the administrator can decide which one he wants to run by inputing the recipe number into the select recipe field and pressing the select recipe button. In case of a mistake, he can press the "KILL" which resets the system and allows for a new recipe input.
### 2.4 Implementation Steps:
The first elements to be implemented were the measuring gauges for the current position of the robotic arm, although not in their current form. The main challenge was trying to represent the 3D space the robotic arm covers in a 2D screen. At first we had a unique system of a rectangle that expanded in different directions based on the movement. It was a rather unintuitive system that displayed each position uniquely. The system was abandoned when a software issue caused us to lose all of our SCADA progress, and we opted for the simpler gauge system we have now. After reimplementing the arm monitoring, we implemented item tracking without mojor setbacks. Next we implemented recipe selection for which we had trouble getting the numeric input field to actually commit the change. We fixed that problem by implementing a separate commit button, which we later used for all of the editable elements of the SCADA interface. The final big implementation allowing the supervisor to edit recipes, which we struggled with at first as we tried using the "recipe" elements provided in the toolbox of our programming environment. We did not figure out how to implement them correctly and settled for tables instead, which we were already familiar with due to already using them for item tracking. In the last hour we mostly just made it look, slightly better, labeled everything and implemented the "KILL" switch.
Summarize the key stages of the control system's development and testing.

## 3 Instructions for Use

### 3.1 System Startup and Shutdown:
The system is designed in such a way that the supervisor should only need the high level controls provided to him by SCADA, with manual controls serving as backup for various edge-case scenarios. As such, manual control is not accessible via the SCADA interface and has to be accessed via the PLC directly. The system starts in automatic mode by default and will upon receiving power go to initial position. The initial position is defined as having every moving part of the robotic arm at the starting switch. After moving to initial position, it will wait for the supervisor to decide on a recipe he wants the system to execute. When the recipe finishes the arm will return back to its initial position and await the next recipe input. If at any point during the recipe, the supervisor realizes he does not want the recipe to finish, either due to input error or because of safety concerns in unexpected scenarios, he can press the "KILL" button, which immediatly terminates the current recipe, returns the arm back to its initial position and allow for a new recipe input.
### 3.2 Manual Mode:
If manual mode becomes a necessity, it can be turned on from the PLC, by flipping the switch. Once in manual mode, the supervisor can control each motor separately, but only one at a time. He selects the motor he wants to control, by pressing the black buttons at the bottom of the PLC. The buttons are mapped to the motors as such:
* Top left black button - Rotation motor
* Top right black button - Exstension motor
* Bottom left black button - Height motor
* Bottom right black button - Grab motor
Once the motor has been selected, the supervisor can control their movement usig the red and green buttons on the left side of the PLC. Moving away from the switch is done by pressing the red button. Moving towards the switch requires both buttons to be pressed simultaneusly. Changing motors and movement directions triggers our one second long interlock, during which the machine will not move. The interlock being active is signaled by the red light turning on in the PLC.
### 3.3 Automatic Mode:
In the case where the SCADA interface is not working correctly, but you still want the robotic arm to execute recipes automatically, you can still have automation controlled directly from the PLC, albeit with limited functionalities. In such a scenario the switch should stay in same upright position as it was when the supervisor was controlling the robotic arm from the SCADA interface. He is no longer able to change recipes, but he is still able to select the recipes he last defined in SCADA, by pressing the black buttons at the bottom of the PLC, where they are mapped as such:
* Top left black button - Recipe 1
* Top right black button - Recipe 2
* Bottom left black button - Recipe 3
If at any point during the recipe, the supervisor realizes he does not want the recipe to finish, either due to input error or because of safety concerns in unexpected scenarios, he can simply flip the switch, which changes the mode to manual and immediatly stops the arm in its position. Then, depending on the reason for the stop, he can either flip the switch back to automatic mode after using manual controls to resolve the immediate issue, which will continue the recipe, or manually move the arm back to its initial position and then flip the switch, which will allow him to select a different recipe.
### 3.4 Alarms and Troubleshooting:
While this should not happen, there is a chance that due to unexpected factors, the motors of the robotic arm can attempt to move it further than it was designed to be. If this happens, a red "!" bubble will appear at the top of the measuring tools displayed in SCADA for that specific motor. In such cases, the KILL switch should be pressed immediatly to prevent endangering the facility, its employees and preventing damages to the robotic arm itself. If the KILL switch does not properly return the arm to the initial position, it is strongly recommended to immediatly halt all proccesses involving the robotic arm and contact our customer service department to get it working correctly again as quickly as possible. We are not legally accountable for any damages and/or injuries that might occur, if the above recomendation is ignored. 

## 4 Implementation Challenges and Solutions

Describe the main difficulties encountered during the project implementation.
Detail how these challenges were addressed, including:
Technical problems and their solutions.
Insights gained from debugging and testing.
Improvements made to initial designs.
Highlight lessons learned and their relevance to future projects.
Conclusion

Recap the project objectives, challenges, and overall results.
## 5 References

List all sources in a standard citation format (e.g., APA, IEEE).
## 6 Appendices (if necessary)

Include supplementary materials, such as:
## 7 System diagrams.
Code excerpts.
Extended test data or results.
