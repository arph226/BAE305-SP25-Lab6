# BAE305-SP25-Lab6
# Laboratory Report for Lab 6 of BAE305 Spring 2025
# Lab 6 - Moving with Purpose: Sensing and Mobility for Basic Robot Applications

* By: Abby Phillips and Audrey Suit
* Submitted: March 12, 2025


# Summary  



# Materials


# Assembly Procedures  


# Test Equipment


# Test Procedures
## Part 1: Learn to Listen (Ultrasonic Sensor Setup)
1. Setup the Ultrasonic Sensor:
    - Connect the ultrasonic sensor HC-SR04 to the RedBoard as shown in the schematic.
    - Trig Pin to pin 11 on the RedBoard.
    - Echo Pin to pin 12 on the RedBoard.
    - Power the RedBoard via USB or external power supply.
2. Open the Arduino IDE:
    - Start the Arduino IDE on your computer.
    - Connect the RedBoard to the computer using the USB cable.
3. Upload the Code:
    - Use the code provided in the lab instructions (or modify the one you have for the ultrasonic sensor) and upload it to the RedBoard.
    - The program will read the distance from the ultrasonic sensor and send the value over serial communication to the computer.
4. Test the Algorithm:
    - Use a ruler to measure the distance between the sensor and an object.
    - Observe the distance readings from the serial monitor.
    - Verify the accuracy and precision of the sensor.
    - Measure the distance for various points (e.g., 5 cm, 10 cm, 20 cm, etc.).
    - Compare the readings from the serial monitor with actual measurements.
5. Test the Precision
    - Review the measurements from the serial monitor and determine the resolution (difference in output for each change in distance).
    - For example, if moving the obstacle by 1mm results in a consistent change in the output value, this is the resolution.

## Part 2.1: Move It (Motor Control and Collision Avoidance)
1. Motor Driver and Motor Setup:
    - Disconnect the ultrasonic sensor from pins 11 and 12 on the RedBoard.
    - Connect the motor driver and two motors to the RedBoard as shown in the schematic (Figure 3).
    - Ensure proper connections between the motor driver, motors, and the RedBoard's control pins.
2. Upload the Motor Control Code:
    - Upload the motor control code to the RedBoard.
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
    - Observe the robot's movement and make adjustments if needed to ensure proper behavior.

## Part 2.2: Integrating Collision Avoidance
1. Integrate the Distance Sensor with Motor Control:
    - Modify the existing motor control code to include the ultrasonic sensor functionality.
    - Use the distance sensor to stop the motors if the distance measured is less than 10 cm.
    - Update the loop() function to continuously check the sensor's distance and stop the motors if an object is detected too close (i.e., within 10 cm).
2. Upload and Test the Code:
    - Upload the modified code to the RedBoard.
    - Place an object in front of the sensor and test the behavior of the robot.
    - The motors should stop when the object is closer than 10 cm from the sensor.
3. Demonstrate the Functionality: Once the system is working correctly, demonstrate the working collision avoidance to the instructor
    - Test the robot's ability to stop when an object is detected in front of it at a distance of less than 10 cm.

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
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

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
<p align="left"><em> Program 1:  </em></p>

**Ultrasonic Sensor Algorithm Verification**
| Set Distance (cm) | Measured Distance (cm)|
|--------------------|--------------------|
|0	|2.56|
|5	|5.13|
|10	|9.91|
|20	|19.35|
|25	|24.11|
|30|	28.86|

<p align="left"><em> Table 1:  </em></p>

**Ultrasonic Sensor Precision**
| Distance Moved (cm) | Measured Distance (cm)|
|--------------------|--------------------|
|0.1	|0.12|
<p align="left"><em> Table 2:  </em></p>

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


//const int driveTime = 10;      //this is the number of milliseconds that it takes the robot to drive 1 inch

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
      driveTime = Serial.readStringUntil('\n');

      int driveTimeInt = driveTime.toInt();
      int distanceInt = distance.toInt();

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
<p align="left"><em> Program 2.1:  </em></p>

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

// Ultrasonic sensor pins
const int trigPin = 6;
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

  pinMode(trigPin, OUTPUT);
  pinMode(echoPin, INPUT);
}

/********************************************************************************/
void loop()
{
  // Sensor reading
  digitalWrite(trigPin, LOW);
  delayMicroseconds(2);
  digitalWrite(trigPin, HIGH);
  delayMicroseconds(10);
  digitalWrite(trigPin, LOW);

  duration = pulseIn(echoPin, HIGH);
  distance = (duration * 0.0343) / 2;

  Serial.print("Distance: ");
  Serial.println(distance);
  
  // Stop motors and prevent further execution if obstacle is too close
  if (distance < 10)
  {
    rightMotor(0);
    leftMotor(0);
    Serial.println("Obstacle detected! Stopping motors.");
    return;  // Exit the loop() function early
  }

  // Only process commands if no obstacle is detected
  if (Serial.available() > 0)
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
<p align="left"><em> Program 2.2:  </em></p>


# Discussion



# Conclusion

