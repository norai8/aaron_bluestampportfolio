# Phone Controlled Robot Arm
Replace this text with a brief description (2-3 sentences) of your project. This description should draw the reader in and make them interested in what you've built. You can include what the biggest challenges, takeaways, and triumphs from completing the project were. As you complete your portfolio, remember your audience is less familiar than you are with all that your project entails!




| **Engineer** | **School** | **Area of Interest** | **Grade** |
|:--:|:--:|:--:|:--:|
| Aaron D. | Monta Vista High School | Computer Science | Incoming Junior


![Headstone Image](logo.svg)  
<!---
# Final Milestone

**Don't forget to replace the text below with the embedding for your milestone video. Go to Youtube, click Share -> Embed, and copy and paste the code to replace what's below.**

<iframe width="560" height="315" src="https://www.youtube.com/embed/F7M7imOVGug" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For your final milestone, explain the outcome of your project. Key details to include are:
- What you've accomplished since your previous milestone
- What your biggest challenges and triumphs were at BSE
- A summary of key topics you learned about
- What you hope to learn in the future after everything you've learned at BSE
-->


# Second Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/y3VAmNlER5Y" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

For my second milestone, I have gotten the app to be able to control the robot arm and make it move with the buttons. This is done through a bluetooth module (HC-06) that is connected to the phone by bluetooth and plugged into the arduino uno. By uploading the code into the arduino uno, I can use the app on the phone to send instructions to the bluetooth module and control the robot. I faced a lot of challenges this milestone in getting the bluetooth module, the app, and the code to work. At first, I tried downloading BlueStacks on my mac, but then realized that the bluetooth module can't connect to a mac. I then downloaded the app on a Google Pixel, and had some problems with the app. When I tried to click a button on the app, it would show an error and after a while I realized that I needed to connect the module again. For my final milestone, I hope to be able to make the robot do multiple actions with one button and also replace the claw on the top of my robot with something else.
# First Milestone

<iframe width="560" height="315" src="https://www.youtube.com/embed/Pt9zTqvawDw" title="YouTube video player" frameborder="0" allow="accelerometer; autoplay; clipboard-write; encrypted-media; gyroscope; picture-in-picture; web-share" allowfullscreen></iframe>

My project is the phone-controlled robotic arm. The first week, I have built the robot as well as attached the controller, and uploaded the code to be able to use the controller to move the arm. Some challenges I faced were that the pieces included while building the arm were not very high quality and easy to break, and the final robot arm was pretty loose too. My plan for week 2 is to download the app and explore the code behind it, and get the app working and able to control the robot. In week 3, I am thinking about adding modifications in the code and maybe even the robot, such as making the robot being able to do 2 movements at once instead of only being able to do one movement at a time with a button. 
<!---
# Schematics 
Here's where you'll put images of your schematics. [Tinkercad](https://www.tinkercad.com/blog/official-guide-to-tinkercad-circuits) and [Fritzing](https://fritzing.org/learning/) are both great resoruces to create professional schematic diagrams, though BSE recommends Tinkercad becuase it can be done easily and for free in the browser. 
-->
# Code

```c++
#include <Servo.h>  // add the servo libraries
Servo myservo1;  // create servo object to control a servo
Servo myservo2;
Servo myservo3;
Servo myservo4;
int pos1=180, pos2=140, pos3=140, pos4=90;  // define the variable of 4 servo angle and assign the initial value( that is the boot posture angle value)
char val;
int incomingByte = 0;          // Received data byte
String inputString = "";         // Used to store received content
boolean newLineReceived = false; // Previous data end flag
boolean startBit  = false;  //Acceptance Agreement Start Sign
int num_reveice=0;
void setup()
{
   // boot posture
  myservo1.write(pos1);  
  delay(1000);
  myservo2.write(pos2);
  myservo3.write(pos3);
  myservo4.write(pos4);
  delay(1500);

  Serial.begin(9600); //  set the baud rate to 9600
}

void loop() 
{
  myservo1.attach(3);  // set the control pin of servo 1 to D3
  myservo2.attach(5);  // set the control pin of servo 2 to D5
  myservo3.attach(6);   // set the control pin of servo 3 to D6
  myservo4.attach(9);   // set the control pin of servo 4 to D9

while (Serial.available())
  {
    incomingByte = Serial.read();              //One byte by byte, the next sentence is read into a string array to form a completed packet
    if (incomingByte == '%')
    {
      num_reveice = 0;
      startBit = true;
    }
    if (startBit == true)
    {
      num_reveice++;
      inputString += (char) incomingByte;     
    }
    if (startBit == true && incomingByte == '#')
    {
      newLineReceived = true;
      startBit = false;
    }
    
    if(num_reveice >= 20)
    {
      num_reveice = 0;
      startBit = false;
      newLineReceived = false;
      inputString = "";
    }  
  }



if(newLineReceived)
{
    switch(inputString[1])   
    {
      case 'B':  T_left();  break;   // turn left
      case 'C':  T_right();  break;//turn right 
      case 'A':  RB();  break;// the lower arm will draw back 
      case 'D':  RF();  break;// the lower arm will  stretch out
      case '5':  ZK();  break;//close the claw
      case '6':  ZB();  break;//close the claw
      case '4':  LB();  break;//the upper arm will lift up 
      case '7':  LF();  break;//the upper arm will go down 
      default:break;
    }

      inputString = "";   // clear the string
      newLineReceived = false;
}   
   




  
}
//**************************************************
// turn left
void T_left()
{
    pos1=pos1+8;
    myservo1.write(pos1);
    delay(5);
    if(pos1>180)
    {
      pos1=180;
    }
}
//turn right 
void T_right()
{
    pos1=pos1-8;
    myservo1.write(pos1);
    delay(5);
    if(pos1<1)
    {
      pos1=1;
    }
}
//********************************************
//close the claw

void ZK()
{
      pos4=pos4-8;
      Serial.println(pos4);
      myservo4.write(pos4);
      delay(5);
      if(pos4<45)
      {
        pos4=45;
      }
}
// open the claw
void ZB()
{
    pos4=pos4+8;
      Serial.println(pos4);
      myservo4.write(pos4);
      delay(5);
      if(pos4>120)
      {
        pos4=120;
      }
}

//******************************************
// the lower arm will  stretch out
void RF()
{
    pos2=pos2-8;
    myservo2.write(pos2);
    delay(5);
    if(pos2<25)
    {
      pos2=25;
    }
}
// the lower arm will draw back 
void RB()
{
    pos2=pos2+8;
    myservo2.write(pos2);
    delay(5);
    if(pos2>180)
    {
      pos2=180;
    }
}

//***************************************
//the upper arm will lift up  
void LB()
{
  pos3=pos3+8;
    myservo3.write(pos3);
    delay(5);
    if(pos3>135)
    {
      pos3=135;
    }
}

//the upper arm will go down  

void LF()
{
  pos3=pos3-8;
    myservo3.write(pos3);
    delay(5);
    if(pos3<0)
    {
      pos3=0;
    }
}

```

# Bill of Materials

| **Part** | **Note** | **Price** | **Link** |
|:--:|:--:|:--:|:--:|
| LAFVIN 4DOF Acrylic Robot Mechanical Arm Claw Kit Compatible with Arduino IDE DIY Robot with CD Tutorial | Base of the robotic arm | $35.99 | <a href="https://www.amazon.com/LAFVIN-Acrylic-Mechanical-Compatible-Tutorial/dp/B07ZYZVNY4"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
| Item Name | What the item is used for | $Price | <a href="https://www.amazon.com/Arduino-A000066-ARDUINO-UNO-R3/dp/B008GRTSV6/"> Link </a> |
<!---
# Other Resources/Examples
One of the best parts about Github is that you can view how other people set up their own work. Here are some past BSE portfolios that are awesome examples. You can view how they set up their portfolio, and you can view their index.md files to understand how they implemented different portfolio components.
- [Example 1](https://trashytuber.github.io/YimingJiaBlueStamp/)
- [Example 2](https://sviatil0.github.io/Sviatoslav_BSE/)
- [Example 3](https://arneshkumar.github.io/arneshbluestamp/)

To watch the BSE tutorial on how to create a portfolio, click here.
-->
