# Instructions
## Introduction
### BBC Micro:bit
The micro:bit is a single board computer (SBC) that contains an application processor with a variety of peripherals. More generally, the Micro:bit can be called a microcontroller. The application processor, also called a microprocessor, is where your program will run (directly from the on-chip flash memory). For the Micro:bit v2, this is a Nordic nRF52833.
https://www.nordicsemi.com/products/nrf52833

The Micro:bit includes a number of different peripherals. Peripherals are sensors, buttons, communication interfaces, etc. Some peripherals that you will be using in this exercise include two buttons, a 5x5 LED matrix, a 2.4Ghz radio, and a motion sensor. Additional peripherals that you might wish to play with include a temperature sensor, a speaker, and a microphone.

### Visual Studio Code
Visual Studio (VS) Code is an integrated development environment (IDE). It is a tool for writing and managing a codebase.Many official and community developed extensions can be installed into your instance of VS Code to customize the application suite your needs and style.

Install VS Code either from the Microsoft Store or from here: https://code.visualstudio.com/

### Micro:bit Python editor
This is an online IDE. It may be easier to develop and test your code here. You can also use this tool to flash your code to the Micro:bit.
https://python.microbit.org/v/3

### Python
Python will be the language of choice here. Python is used widely in industry and hobby projects alike. Install Python via the Microsoft Store. If there is an option for adding Python to PATH, be sure to select ‘yes.’

Open VS Code, navigate to the ‘Extensions’ with the button on the left-pane. Search for ‘Python’, select ‘Python’ extension and install.
This extension will provide you with greater support for Python files such as code auto-completion, formatting assistance, and so forth.

### Git and GitHub
Throughout this exercise, I wish for you to gain an introduction to one of the most powerful tools that any programmer can use, version control. Version control not only allows you to go back in time with your code, but to also develop multiple versions or branches of your code simultaneously. In addition, when properly set up and maintained, version control allows for any number of people to contribute to a single codebase in parallel.

The version control system that you are to use is called Git. Download and install it from: https://git-scm.com/

GitHub is an online tool for storing your code. It is especially useful when multiple developers are working on the same code base simultaneously. It also makes it easy to share your code with other people. Create yourself a public GitHub account: https://github.com/

## Software
### Your repository
- Find the repo here: https://github.com/richard-jh-mccrae/micro-bit_car
- Fork the repository, which is to say, make yourself your own copy to be maintained under your GitHub account. Do this via the GitHub web client.
- Clone (download) the newly made for to your PC using the Git Bash application. Unless you already have experience with version control, keep things simple by working only from the ‘main’ branch. You’ll commit (save) your code directly into just this branch. At the end of this week, I’ll teach you about branching, pull requests, and code review.
- Create a new directory within your repository’s local copy called ‘src’. Within src, create a new Python file called remote_control.py.

### MicroPython and Microbit
MicroPython and Microbit are Python application programming interfaces (API) that you will depend on. In your programs, you will import the `microbit` Python module. The microbit module is a specialized library that operates on top of MicroPython, providing a simplified API tailored to the Micro:bit's unique hardware capabilities.

Documentation: https://microbit-micropython.readthedocs.io/en/v1.1.1/tutorials/introduction.html

### Super loop
Your application will use a super loop software architecture. Using a super loop is common in beginner embedded projects. It is an endless loop that contains all code for achieving a desired range of functionality over and over again. If peripheral initialization is required, this happens before the endless loop.

Such architectures are easy to understand, however they suffer in terms of responsiveness and fault tolerance. If there is time at the end of this week, you can move away from the super loop to use interrupts.

An example super loop might look something like this:

```python
import microbit

def init_something():
	# Initialize something

def teardown_something():
    # teardown code upon failure

init_something()

while (True): # Run until failure
	try:
        if button_a.is_pressed():
            # Turn on LED
            # Sleep 1 second
        else:
            # Turn off LED
    except:
        # A horrible exception has been raised! Jump out of while lopp
        break

teardown_something()
```

- Using a super loop, make a Hello World program using the LED matrix and a button press. Each press of the button will scroll “Hello World.” Flash the program to your device and verify that it works.

## Transmitter program
With your toolchain setup and flashing working, begin writing the software for your remote controller. How exactly you wish to control the car is up to you. The only requirement is that you utilize a minimum of two peripherals, for example the two buttons and the accelerometer. **Please do not use the buzzer yet, as this is very obnoxious for everyone else in the office.**

Find relevant APIs in the documentation linked above.

- Based on user input, display arrows on transmitter's LED matrix. You decide the implementation. For example, tilting the device to the left causes a left arrow to show, while pressing buttons A or B drives the car forward or backwards. 

    Play around with possible solutions. How responsive are they? What happens when you press the buttons very quickly while tilting the device? What happens when both buttons are pressed at the same time or while the device is positioned upside-down? What if you shake the controller or drop it? 

    It may be difficult to get the level of responsiveness that you expect when handling multiple user inputs simultaneously. This is the disadvantage of a super loop. Keep working with your code until you are satisfied. Ask Google, ask ChatGPT. 
- When you are satisfied with your program, implement the radio module. Each user input shall be sent over radio to the receiving device for further processing. Again, find information about the radio module in the documentation.

## Receiver Program
Take a break to install the following software. We will use this to measure voltage levels on the receiver’s pins

Analog Discovery: https://digilent.com/shop/software/digilent-waveforms/

- Implement the radio module on the receiver to accept commands from the transmitter. Then instruct the device to show the same images on its LED matrix as the transmitter. 

    Is there a delay? If so, can this be improved? Consider how your transmitter is handling multiple commands simultaneously. **Only move on to the next step when you are completely satisfied with your implementation.**
- Program the device to generate the digital outputs necessary to drive the motors. This will require studying your hardware’s datasheets. You will control the four motors as two sets of two. The right-side motors will be controlled together as one, same for the left. The motors can run in both directions, and their speeds can be controlled.

    DC motors tend to consume a great deal of current, more than what 3-5V microcontrollers can typically deliver through their pins. To overcome this, an H-bridge will be used. One H-bridge per motor.

    An H-bridge will allow the Micro:Bit to control the direction and speed of the motors without needing to supply them power. The bridge will take power from the AA battery pack.

    The TB6612FNG Dual DC motor drive is an integrated circuit with two H-bridges. You’ll use one TB6612FNG for the left two motors, one for the right two motors. The TB6612FNG accepts two direction controls per motor (IN1 and IN2 pins), while speed is controlled through the pulse-width-modulation pin (PWM). There is a standby (STBY) pin for enabling/disabling the TB6612FNG, as well as power and ground pins (VCC and GND). Motor output pins are labeled O1 and O2. These will be connected to the motors’ positive and negative leads.

    - Go to the TB6612FNG’s datasheet found in the repo’s documents folder. Search for the table titled H-SW Control Function. This gives an overview of the pin values required to drive the motors as you wish.

    **Note that you are not connecting the Micro:bit to the TB6612FNGs just yet.** You’ll do this in the next section. You will start by testing the digital output signals with the Analog Discovery measurement device.	

- Write the program to output the required digital signals to control the motors. Skip PWM until the next step. It might be useful during the start to maintain the LED matrix showing direction arrows.

    Help yourself by creating a pin table, for example:
    | Micro:bit Pin | Motor drive Pin |
    |---------------|-----------------|
    | PIN1          | PIN_A           |
    | PIN2          | PIN_B           |
    | ...           | ...             |

    Write the code for one pin at a time, measuring the voltage output with the Analog Discovery. View the signal in the WaveForm application. Again, consider the responsiveness of your system. Is there a delay from the user input on the transmitter to a pin output on the receiver?

- Pulse-width modulation (PWM):
    The TB6612FNG accepts a PWM signal from the Micro:bit to control the speed of the motors. The Micro:bit can only supply PWM signals from its three dedicated PWM pins.

    Pulse-width modulation allows one to control precisely how long a pin’s digital output is high and low. For example, you can program the PWM pin to output 3.3V for 500ms and 0V for 100ms repeatedly. This pin is now only supplying 5/6 of its total energy, 2.75V instead of 3.3V.

    When the signal is high, we call this "on time". To describe the amount of "on time”, we use the concept of duty cycle. The duty cycle is measured in percentages. The percentage duty cycle specifically describes the percentage of time a digital signal is on over an interval or period.

    If a digital signal spends half of the time on and the other half off, we would say the digital signal has a duty cycle of 50%. If the percentage is higher than 50%, the digital signal spends more time in the high state than the low state and vice versa if the duty cycle is less than 50%. Here is a graph that illustrates these three scenarios:

    -   Search the MicroBit-MicroPython documentation for generating pulse-width modulated signals, and implement the code.

    Study both the datasheet of the H-bridge and that of the micropython API to achieve your desired motor speed. An easy solution is to hard code the speed to be always at its maximum. I wish for you to implement a means for the user to actively control the motor speed while driving.

    -   Measure your PWM signals with the Analog Discovery.

## Hardware
With your software working as you’d like, you can move onto connecting all of the hardware components.

- Begin by drawing a schematic. To do this, you must reference your MicroBit’s pinout diagram and the TB6612FNG’s datasheet.
    - If you wish to take the time, you can create your schematics in a programm like Fritzing, and save the image in your repository. You can them show this image in your project's readme file.

        https://fritzing.org/

- Connect your components.

    It might be a good idea to connect a single motor to the TB6612FNG, and then this to the Micro:bit. Then test that the motor is responding as expected before connecting all of the rest.

    For some help, see here https://learn.sparkfun.com/tutorials/tb6612fng-hookup-guide/all
- Play with your car!

    Make any SW improvements that you wish.

    Perhaps take a video of the car driving, which you can upload and show in your project's readme file.

### Bonus questions:
- Are the AA batteries in series or parallel?
- How many total volts will the AA batteries deliver? 
- How much current capacity can the AA batteries provide, measured in milli-Amp hours (mAh)?
- What is the average current consumption per motor given the battery voltage output?
- How much current can the Micro:bit deliver in total? What is the max available current output per pin?
- With four motors driving at full-speed non-stop until the AA batteries die (ignore Micro:bit power consumption as its negligible here), how long until you expect the AA batteries will die?

## Version 2
To illustrate the power of software version control, create a new version of your software.

- Pretend that after you have everything working as you wish, you decide that you want to change how a user inputs commands to the transmitter. For example, if you use buttons and tilting to control the car and wish instead to use voice commands. Develop this new line of code in what is called a branch. By developing in a separate branch, your first SW version remains un-touched and stable while you rework the code.

To make it easier for you future self, be certain that your commit messages clearly states what is being changed.

Practice switching between your two branches with git checkout. See https://git-scm.com/book/en/v2/Git-Branching-Branches-in-a-Nutshell

- Once your new feature is complete, create a pull request (PR) on GitHub to merge the branch into your main line code. A PR would be less confusing if it were named “merge request,” which is exactly what it is. You are requesting to merge the code changes from ‘branch A’ into your main line code (‘main’ branch).

I’ll then walk you through a basic PR review process on GitHub. After completing the review process, merge your branch into main.

- On your laptop, switch from your feature branch to main, and update the branch with a ‘git pull’ command. Your local copy of main should now be up to date with what is on GitHub. You can delete your local copy of your new branch that you made.

- View your commit history both on GitHub and on your machine via the command line. Use ‘git log’: https://git-scm.com/docs/git-log
