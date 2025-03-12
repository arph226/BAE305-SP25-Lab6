# BAE305-SP25-Lab6
# Laboratory Report for Lab 6 of BAE305 Spring 2025
# Lab 6 - Moving with Purpose: Sensing and Mobility for Basic Robot Applications

* By: Abby Phillips and Audrey Suit
* Submitted: March 12, 2025


# Summary  

In this lab, we combined sensor interfacing with motor control to build a basic robotic system capable of collision avoidance. Our objectives were to learn how to use an ultrasonic sensor for distance measurement and to control motor movement via an H-Bridge motor driver using the Arduino platform. We started by assembling the HC-SR04 ultrasonic sensor and writing code to capture distance data, verifying the sensor’s resolution and precision with ruler tests. Next, we integrated the sensor with a motor driver and two motors, programming an algorithm to control motor speed and direction at various speeds (including commands for turning and reversing) to prevent collisions. This hands-on experience reinforced the integration of analog and digital sensor inputs into a dynamic control system, similar to the collision avoidance systems used in modern vehicles. Overall, the lab provided a strong foundation in real-time sensing and mobility control, key components for developing more advanced robotic applications.

# Materials
Computer running Arduino IDE
Sparkfun Inventor's Kit: RedBoard, ultrasonic sensor HC-SR04, two motors, motor driver, standard wires

# Assembly Procedures  

### Part 1 - Learn to Listen

To start, we built the ultrasonic sensor circuit shown in Figures 1.1 and 1.2. 

![image](https://github.com/user-attachments/assets/b834b017-79fc-4097-9e09-9e1aa49d80a4)

![image](https://github.com/user-attachments/assets/07efb9cb-0f47-4c75-a698-2548d28765c3)


After launching Arduino IDE on the computer, we selected our Arduino Uno following the menu (Tools>Board>Arduino Uno) and connected the corresponding COM port through (Tools>Port>COM1). 

### Part 2 - Move It

For this section, we built a new circuit shown in Figures 2.1, 2.2, and 2.3. We kept the ultrasonic sensor from Part 1 and moved it down to Rows 22-25. We also moved the sensor connections to pins 7 and 6. 

![image](https://github.com/user-attachments/assets/cb0ebc97-663e-40f2-a6ba-28f4b47a4da1)

![image](https://github.com/user-attachments/assets/dec67ddd-f55a-487d-b39d-22821a3a2270)

![image](https://github.com/user-attachments/assets/3b2e5a97-eaa0-45ea-8db3-04889987e178)



# Test Equipment
Standard straightedge ruler

# Test Procedures
## Part 1: Learn to Listen 
1. Setup the Ultrasonic Sensor:
    - Connect the ultrasonic sensor HC-SR04 to the RedBoard as shown in Figure 1 in the Assembly Procedures.
    - Trig Pin to pin 11 on the RedBoard.
    - Echo Pin to pin 12 on the RedBoard.
    - Power the RedBoard via USB to easily upload the code
2. Open the Arduino IDE:
    - Start the Arduino IDE on your computer.
    - Connect the RedBoard to the computer using the USB cable.
3. Upload the Code:
    - Use the code provided in the lab instructions and online in the Adruino Project Hub and upload it to the RedBoard. See Program 1 in the results for our code.
    - The program will read the distance from the ultrasonic sensor and send the value over serial communication to the computer. Test to make sure the serial monitor is reading values
4. Test the Algorithm:
    - Use a ruler to measure the distance between the sensor and an object. Be sure to have the ruler at the same starting point each time. There will be a gap where the sensor ends and the ardino base, so account for this difference.
    - Observe the distance readings from the serial monitor.
    - Verify the accuracy and precision of the sensor by measuring the distance for various points (e.g., 5 cm, 10 cm, 20 cm, etc.) and compare the readings from the serial monitor with actual measurements. These results are shown in Table 1.
5. Test the Precision
    - Review the measurements from the serial monitor and determine the resolution (difference in output for each change in distance).
    - To test this we moved the breadboard we were using to block the sensor from 5 cm away to 5.1 cm and recorded this difference. In the future, we would test more than one measurement for this.

## Part 2.1: Move It 
1. Motor Driver and Motor Setup:
    - Disconnect the ultrasonic sensor from pins 11 and 12 on the RedBoard and move it to pins 7 and 6.
    - Connect the motor driver and two motors to the RedBoard as shown in the schematic (Figure 2).
    - Ensure proper connections between the motor driver, motors, and the RedBoard's control pins by cross checking between the circuit and schematic
2. Upload the Motor Control Code:
    - Upload the motor control code to the RedBoard. We used the code from the lab procedures (sparkfun) as a base and has Chatgpt help us with any errors we encountered.
    - The code should allow you to control the direction and speed of the motors via serial commands. 
3. Test Motor Movement:
    - Use serial commands to control the motors. For example:
      - "f 50 50" (move forward for 50 units at speed 50)
      - "b 50 50" (move backward for 50 units at speed 50)
      - "r 90 50" (turn right 90 degrees at speed 50)
      - "l 90 50" (turn left 90 degrees at speed 50)
    -  Test each direction (forward, backward, left, and right) and confirm that the motors respond accordingly.
4. Adjust Motor Speed:
    - Test the motors at three different speeds (slow, medium, fast) by changing the speed values (e.g., 30, 50, 100).
    - Document the speeds that correspond to these values and observe the performance of the motors.
5. Determine the Minimum Speed for Movement:
    - Experiment to determine the minimum motor speed at which the motors begin to move forward.
    - This is typically the lowest speed that results in visible movement.
6. Testing Turns and Backward Movement:
    - Use the motor control program to make the robot turn right, turn left, and move backward at various speeds.

## Part 2.2: Integrating Collision Avoidance
1. Integrate the Distance Sensor with Motor Control:
    - Modify the existing motor control code to include the ultrasonic sensor functionality.
    - Use the distance sensor to stop the motors if the distance measured is less than 10 cm.
    - Update the loop() function to continuously check the sensor's distance and stop the motors if an object is detected too close (i.e., within 10 cm).
2. Upload and Test the Code:
    - Upload the modified code to the RedBoard.
    - Place an object in front of the sensor and test the behavior of the robot.
    - The motors should stop when the object is closer than 10 cm from the sensor.

# Test Results
## Part 1: Learn to Listen
```ruby
//define pins trig and echo connected to
const int trigPin = 11;
const int echoPin = 12;

//declare 2 floats, duration and distance
float duration, distance;

// trig as output, echo as input, start serial monitor
void setup() {
  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
  Serial.begin(9600);
}

//set trig low for 2 seconds, then high 10 micro seconds
void loop() {
  digitalWrite(trigPin, LOW); //sets the trigger pin to LOW, ensuring no signals are being sent.
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH); // Sends a high pulse (ultrasonic wave) for 10 microseconds
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW); //stops pulse

//store time echo pin goes high as suration
  duration = pulseIn(echoPin, HIGH);

  //duration to distance with speed of sound
  distance = (duration*.0343)/2;

  //results to serial monitor
  Serial.print("Distance: ");
  Serial.println(distance);
  delay(100);
}
```
<p align="left"><em> Program 1: The above code triggers the ultrasonic sensor using trigPin, waits for the echo from echoPin, and calculates the distance to the object using the speed of sound. The code then displays the distance and monitors every 100 ms. The program completes these steps in 4 main parts. It defines the pins on the arduino that send and recieve the ultrasonic signal (trigPin and echoPin), and declares the variables duration and distance through a float. Then, the program sets up the trigger pin and echo pin as outputs and inputs to recieve signals. Finally, the loop function continuously to send pulses and measure the time for the pulse to travel back. It then converts this measurement to a distance. </em></p>

**Ultrasonic Sensor Algorithm Verification**
| Set Distance (cm) | Measured Distance (cm)|
|--------------------|--------------------|
|0	|2.56|
|5	|5.13|
|10	|9.91|
|20	|19.35|
|25	|24.11|
|30|	28.86|

<p align="left"><em> Table 1: This table shows the set distance a breadboard is placed away from the sensor, measured by a ruler, compared to the distance measured by the sensor and printed by the serial monitor. As shown, these values are very close, with only slight errors that can be contributed to discrepancies in the precision of measurement with a ruler.  </em></p>

**Ultrasonic Sensor Precision**
| Distance Moved (cm) | Measured Distance (cm)|
|--------------------|--------------------|
|0.1	|0.12|
<p align="left"><em> Table 2: This table shows a comparison of change in distance measured by a ruler compared to measured distance via the sensor. We tested this by moving the blocking object from 5cm away to 5.1 cm and saw a measured distance change of 0.12 cm. This is very close to the expected value (0.1cm). We can test more values to get a better understanding of the precision of the sensor. </em></p>

## Part 2: Move It
```ruby
//the right motor will be controlled by the motor A pins on the motor driver
const int AIN1 = 13;           //control pin 1 on the motor driver for the right motor
const int AIN2 = 12;            //control pin 2 on the motor driver for the right motor
const int PWMA = 11;            //speed control pin on the motor driver for the right motor

//the left motor will be controlled by the motor B pins on the motor driver
const int PWMB = 10;           //speed control pin on the motor driver for the left motor
const int BIN2 = 9;           //control pin 2 on the motor driver for the left motor
const int BIN1 = 8;           //control pin 1 on the motor driver for the left motor

const int turnTime = 8;        //this is the number of milliseconds that it takes to turn the robot 1 degree

String botDirection;           //the direction that the robot will drive in (this change which direction the two motors spin in)
String distance;               //the distance to travel in each direction
String driveTime;

/********************************************************************************/
void setup()
{

  //set the motor control pins as outputs
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);
  pinMode(PWMA, OUTPUT);

  pinMode(BIN1, OUTPUT);
  pinMode(BIN2, OUTPUT);
  pinMode(PWMB, OUTPUT);

  Serial.begin(9600);           //begin serial communication with the computer

  //prompt the user to enter a command
  Serial.println("Enter a direction followed by a distance followed by speed.");
  Serial.println("f = forward, b = backward, r = turn right, l = turn left");
  Serial.println("direction distance speed");
  Serial.println("Example command: f 50 50");
}

/********************************************************************************/
void loop()
{

  {                                                     //if the switch is in the ON position
    if (Serial.available() > 0)                         //if the user has sent a command to the RedBoard
    {
      botDirection = Serial.readStringUntil(' ');       //read the characters in the command until you reach the first space
      distance = Serial.readStringUntil(' ');           //read the characters in the command until you reach the second space
      driveTime = Serial.readStringUntil('\n');        // read the characters in the command until a newline character is entered

      int driveTimeInt = driveTime.toInt();            //converts the sring driveTime into an integer
      int distanceInt = distance.toInt();           //converts the sring distance into an integer

      //print the command that was just received in the serial monitor
      Serial.print(botDirection);
      Serial.print(" ");
      Serial.println(distanceInt);
      Serial.print(driveTimeInt);

      if (botDirection == "f")                         //if the entered direction is forward
      {
        rightMotor(driveTimeInt);                                //drive the right wheel forward
        leftMotor(driveTimeInt);                                 //drive the left wheel forward
        delay(driveTimeInt * distanceInt);            //drive the motors long enough travel the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "b")                    //if the entered direction is backward
      {
        rightMotor(-driveTimeInt);                               //drive the right wheel forward
        leftMotor(-driveTimeInt);                                //drive the left wheel forward
        delay(driveTimeInt * distanceInt);            //drive the motors long enough travel the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "r")                     //if the entered direction is right
      {
        rightMotor(-driveTimeInt);                               //drive the right wheel forward
        leftMotor(driveTimeInt);                                 //drive the left wheel forward
        delay(turnTime * distance.toInt());             //drive the motors long enough turn the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
      else if (botDirection == "l")                   //if the entered direction is left
      {
        rightMotor(driveTimeInt);                                //drive the right wheel forward
        leftMotor(-driveTimeInt);                                //drive the left wheel forward
        delay(turnTime * distance.toInt());             //drive the motors long enough turn the entered distance
        rightMotor(0);                                  //turn the right motor off
        leftMotor(0);                                   //turn the left motor off
      }
    }
  }
 
  
}
/********************************************************************************/
void rightMotor(int motorSpeed)                       //function for driving the right motor
{
  if (motorSpeed > 0)                                 //if the motor should drive forward (positive speed)
  {
    digitalWrite(AIN1, HIGH);                         //set pin 1 to high
    digitalWrite(AIN2, LOW);                          //set pin 2 to low
  }
  else if (motorSpeed < 0)                            //if the motor should drive backward (negative speed)
  {
    digitalWrite(AIN1, LOW);                          //set pin 1 to low
    digitalWrite(AIN2, HIGH);                         //set pin 2 to high
  }
  else                                                //if the motor should stop
  {
    digitalWrite(AIN1, LOW);                          //set pin 1 to low
    digitalWrite(AIN2, LOW);                          //set pin 2 to low
  }
  analogWrite(PWMA, abs(motorSpeed));                 //now that the motor direction is set, drive it at the entered speed
}

/********************************************************************************/
void leftMotor(int motorSpeed)                        //function for driving the left motor
{
  if (motorSpeed > 0)                                 //if the motor should drive forward (positive speed)
  {
    digitalWrite(BIN1, HIGH);                         //set pin 1 to high
    digitalWrite(BIN2, LOW);                          //set pin 2 to low
  }
  else if (motorSpeed < 0)                            //if the motor should drive backward (negative speed)
  {
    digitalWrite(BIN1, LOW);                          //set pin 1 to low
    digitalWrite(BIN2, HIGH);                         //set pin 2 to high
  }
  else                                                //if the motor should stop
  {
    digitalWrite(BIN1, LOW);                          //set pin 1 to low
    digitalWrite(BIN2, LOW);                          //set pin 2 to low
  }
  analogWrite(PWMB, abs(motorSpeed));                 //now that the motor direction is set, drive it at the entered speed
}
```
<p align="left"><em> Program 2.1: The above program controls two motors using a motor driver allowing it to move forward, backward, and turn based on user input via the serial monitor. The motors speed and direction are controlled using digital and PWM signals as communicated by the user in the serial monitor. These commands are made by the inputs direction distance speed as defined by botDirection, distance, and driveTime. More detail is shown in the comments throughout the program above. </em></p>

```ruby
// Motor control pins
const int AIN1 = 13;            
const int AIN2 = 12;            
const int PWMA = 11;            

const int PWMB = 10;           
const int BIN2 = 9;           
const int BIN1 = 8;           

const int turnTime = 8;       

String botDirection;           
String distanceStr;               
String driveTimeStr;

const int trigPin = 6;        // Ultrasonic sensor pins
const int echoPin = 7;

float duration, distance;

/********************************************************************************/
void setup()
{
  pinMode(AIN1, OUTPUT);
  pinMode(AIN2, OUTPUT);
  pinMode(PWMA, OUTPUT);

  pinMode(BIN1, OUTPUT);
  pinMode(BIN2, OUTPUT);
  pinMode(PWMB, OUTPUT);

  Serial.begin(9600);

  Serial.println("Enter a direction followed by a distance followed by speed.");
  Serial.println("f = forward, b = backward, r = turn right, l = turn left");
  Serial.println("Example command: f 50 50");

  pinMode(trigPin, OUTPUT);        //sets up trigPin as output
  pinMode(echoPin, INPUT);        // sets up echoPin as input
}

/********************************************************************************/
void loop()
{
  // Sensor reading and pulse sending
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration * 0.0343) / 2;

  Serial.print("Distance: ");
  Serial.println(distance);
  
  
  if (distance < 10)          // Stop motors and prevent further execution if obstacle is too close
  {
    rightMotor(0);
    leftMotor(0);
    Serial.println("Obstacle detected! Stopping motors.");        // print warning command
    return;  // Exit the loop() function early
  }

  
  if (Serial.available() > 0)  // Only process commands if no obstacle is detected
  {
    botDirection = Serial.readStringUntil(' ');
    distanceStr = Serial.readStringUntil(' ');
    driveTimeStr = Serial.readStringUntil('\n');

    int driveTimeInt = driveTimeStr.toInt();
    int distanceInt = distanceStr.toInt();

    Serial.print(botDirection);
    Serial.print(" ");
    Serial.println(distanceInt);
    Serial.print(driveTimeInt);

    if (botDirection == "f")
    {
      rightMotor(driveTimeInt);
      leftMotor(driveTimeInt);
      delay(driveTimeInt * distanceInt);
    }
    else if (botDirection == "b")
    {
      rightMotor(-driveTimeInt);
      leftMotor(-driveTimeInt);
      delay(driveTimeInt * distanceInt);
    }
    else if (botDirection == "r")
    {
      rightMotor(-driveTimeInt);
      leftMotor(driveTimeInt);
      delay(turnTime * distanceInt);
    }
    else if (botDirection == "l")
    {
      rightMotor(driveTimeInt);
      leftMotor(-driveTimeInt);
      delay(turnTime * distanceInt);
    }
    
    rightMotor(0);
    leftMotor(0);
  }
}

/********************************************************************************/
void rightMotor(int motorSpeed)
{
  if (motorSpeed > 0)
  {
    digitalWrite(AIN1, HIGH);
    digitalWrite(AIN2, LOW);
  }
  else if (motorSpeed < 0)
  {
    digitalWrite(AIN1, LOW);
    digitalWrite(AIN2, HIGH);
  }
  else
  {
    digitalWrite(AIN1, LOW);
    digitalWrite(AIN2, LOW);
  }
  analogWrite(PWMA, abs(motorSpeed));
}

/********************************************************************************/
void leftMotor(int motorSpeed)
{
  if (motorSpeed > 0)
  {
    digitalWrite(BIN1, HIGH);
    digitalWrite(BIN2, LOW);
  }
  else if (motorSpeed < 0)
  {
    digitalWrite(BIN1, LOW);
    digitalWrite(BIN2, HIGH);
  }
  else
  {
    digitalWrite(BIN1, LOW);
    digitalWrite(BIN2, LOW);
  }
  analogWrite(PWMB, abs(motorSpeed));
}
```
<p align="left"><em> Program 2.2: This program uses the code outlined in Program 2.1 and incorporated feedback from the ultrasonic sensor to turn off the motor if it is within 10cm of an object. It does this by defining the sensor pins (trigPin and echoPin) and integrating them into the loop as as logic statement. When the sensor detects an object less than 10cm away, it stops the motors with rightMotor(0) and leftMotor (0) and prints a warning statment. It is important to note we were unable to find a way for the motors to stop while a function is running since the Arduino can only run one command at a time. Therefore, if an object was detected, the motors would continue moving until they finished their command, and then would not proceed with any further commands until the object was moved out of the way.   </em></p>


# Discussion
Discussion Question 1: What is the minimum speed number for the motors to move forward?


# Conclusion

