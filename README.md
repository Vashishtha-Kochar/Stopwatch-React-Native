# Stopwatch using React Native
I have built a fully functional stopwatch using React Native. This app can work on Android as well as iOS.
This project has been made following the YouTube tutorial by React Native Academy : https://www.youtube.com/watch?v=gWegskGYCtA .

# Application Design
The app consists of three screens.

On the initial screen you can see the timer set to zeroes and two buttons: the lap button and the start button. The lap button is disabled until you start the timer with the Start button.

Once it is running, You can press the lap button to measure the split times. When there are at least three laps, The slowest and the fastest intervals are highlighted. The current lap increases together with the main timer.

When the stopwatch is running, you can stop it. From here you can resume or reset. Reset brings you back to the initial screen.

# The components
**App**, the top component, houses all the other components.

**Timer** displays current time measurement.

**ButtonsRow** display individual Buttons arranged horizontally.

**RoundButton** is this circle button component.

Next we’ve got **LapsTable** - which is a scrollable list of individual Laps.

Each **Lap** component displays the measurement of a single interval.

# How it was made
We started with the static designs and broke them down into static omponents. Based on that we implemented the user interface of the app.
After adding every component, we modify the **styles**, which is a stylesheet, and add/modify a style.

We need to parse the timer reading into minutes, seconds and fractions of the second. I will add **moment** library for that. **Moment** is the most popular JavaScript library to parse and manipulate dates.

To make RoundButton circular, set **borderRadius** in the styleSheet. 

For buttonsRow, we set flexDirection to row and set justifyContent to space-between so that the buttons are spread to the sides of the screen. We also need to set alignSelf to stretch so that the ButtonsRow component takes whole available space.

Then, we define a component for an individual lap. It takes two properties - **number** (lap number) and **interval** (its duration).

Next goes the **LapsTable** component.
Start by wrapping the contents with the **ScrollView** so that it will be possible to browse the laps if they don’t fit the screen. 
We map elements inside the laps array into Lap elements. **Interval** is lap duration in milliseconds. 
We calculate the lap number by subtracting lap index inside the laps array from the total number of laps. The **number** identifies the lap uniquely so we use it as a key. 
For laps duration we could reuse the **Timer** component. 
To highlight the fastest and the slowest lap, we will add two new properties to the lap component: **fastest** and **slowest**. They will be applied conditionally if the corresponding property is true. 
We extract lap style into a constant and apply the logic. 
We start by extracting all the lap but the first one. The slice method returns a copy of the original laps array, starting from the first element to the end, without modifying the original laps array.

Next we designed the application state and handled interactions.

The first step will be to design UI state. In React we use component state to control the data that changes over time. It is important to identify which components owns that data. Usually it is the top level component that passes the data to its children components as props. In our case data will owned be the App component.
To calculate the duration we need to store the time when it starts measuring. We add the **start** property to the **state** and assume initial value of 0. To calculate the duration We also need the current time, which will be stored in **now**.
**start** and **now** will be represented as a number of milliseconds since the Empoch time, which is January 1st 1970. This is what **getTime** method of the Date object returns in JavaScript.

We define the **start** class field - it represents the handler function. We pass it as the property to the **Start** button. We also add the **Lap** button and comment out the **Reset** button - we don’t need it now. The **Lap** button should be disabled when the timer is not running. I don’t want it to handle any events or change opacity when pressed. This will be handled by the **disabled** property.
We use **TouchableOpacity** to render the **RoundButton**. I add **onPress** and **disabled** properties to the **RoundButton**. **onPress** is passed to the **TouchableOpacity**, which can handle it. If a button is disabled, it’s opacity is always 1. If this is the case, **onPress** callback should not be executed.

The **timer** should be updated on a continuous basis. We use **setInterval** function to update **state.now** property.

You can notice that the **timer** jumps left and right. This is becase the font that we use is not monospaces - digits have different width. Also if any of the timer parts is less than 10 we should add a leading zero. So we define the **pad** function.
To fix the **Timer** we wrap **minutes**, **seconds** and **centiseconds** with their own Text components with fixed width. The whole timer is placed inside a View that has **flexDirection** set to **row**. Now the style is applied to each element.

We make the 3 sets of **ButtonsRow**, which will be rendered at specific times when corresponding conditions are fulfilled.

The last thing to do is to clear the **timer*** when the **App** component unmounts so that all allocated resources are released.
