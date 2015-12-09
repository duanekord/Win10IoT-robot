# Win10IoT-robot
Raspberry Pi 2 with Windows 10 IoT Robot - C#

To see the robot in action you can click the picture below or here: [https://youtu.be/aYj2wNEQxco](https://youtu.be/aYj2wNEQxco)

[![Alt text for your video](http://www.compoexpress.com/media/catalog/product/cache/2/image/500x500/e6f6636d1b4b3c56f73fd0ae52afb1ff/f/e/feeyb_conew1_1_.jpg)](https://youtu.be/aYj2wNEQxco)

I used the Raspberry Pi 2 Model B running Windows IoT

![alt tag](http://i66.tinypic.com/29nvxc9.jpg)

This robot kit: (any robot kit with 2 motors will work)

![alt tag](http://i67.tinypic.com/5be2a9.jpg)

I used this USB Keyboard/Mouse combo from Amazon ($15) (any one will work)
![alt tag](http://i68.tinypic.com/2qjbmz5.jpg)



#####Hardware Setup
![alt tag](http://i64.tinypic.com/sw3fpd.jpg)



#####Code
This code is written headed but should be used in headless mode.  Using the Up, Down, Left, Right or W, A, S, D buttons to control your robot.  This was written as a Unviversal Windows App but can be ran a a headless IoT App.

Add this `using Windows.UI.Core;` statement to the top of your code to read keyboard presses.

Declare variables and set Pin #'s:

		
		private const int leftE_PIN = 25;
		private const int left1_PIN = 12;
		private const int left2_PIN = 16;
		private const int rightE_PIN = 18;
		private const int right1_PIN = 24;
		private const int right2_PIN = 23;

		private GpioPin leftE;
		private GpioPin left1;
		private GpioPin left2;
		private GpioPin rightE;
		private GpioPin right1;
		private GpioPin right2;

		GpioPinValue pinValueHigh = GpioPinValue.High;
		GpioPinValue pinValueLow = GpioPinValue.Low;



        public MainPage()
        {
            InitGPIO();
            this.InitializeComponent();
            CoreWindow.GetForCurrentThread().KeyDown += Window_KeyDown;
            CoreWindow.GetForCurrentThread().KeyUp += Window_KeyUp;
        }

        private void InitGPIO()
        {
            //Gets the default GPIO if there is only one
            GpioController gpio = GpioController.GetDefault();


            // Show an error if there is no GPIO controller
            if (gpio == null)
            {
                leftE = null;
                left1 = null;
                left2 = null;
                rightE = null;
                right1 = null;
                right2 = null;

                return;
            }

            leftE = gpio.OpenPin(leftE_PIN);
            left1 = gpio.OpenPin(left1_PIN);
            left2 = gpio.OpenPin(left2_PIN);
            rightE = gpio.OpenPin(rightE_PIN);
            right1 = gpio.OpenPin(right1_PIN);
            right2 = gpio.OpenPin(right2_PIN);

            leftE.Write(pinValueHigh);
            left1.Write(pinValueHigh);
            left2.Write(pinValueHigh);
            rightE.Write(pinValueHigh);
            right1.Write(pinValueHigh);
            right2.Write(pinValueHigh);

            leftE.SetDriveMode(GpioPinDriveMode.Output);
            left1.SetDriveMode(GpioPinDriveMode.Output);
            left2.SetDriveMode(GpioPinDriveMode.Output);
            rightE.SetDriveMode(GpioPinDriveMode.Output);
            right1.SetDriveMode(GpioPinDriveMode.Output);
            right2.SetDriveMode(GpioPinDriveMode.Output);

        }

        private void defaultPins()
        {
            leftE.Write(pinValueHigh);
            left1.Write(pinValueHigh);
            left2.Write(pinValueHigh);
            rightE.Write(pinValueHigh);
            right1.Write(pinValueHigh);
            right2.Write(pinValueHigh);

            leftE.SetDriveMode(GpioPinDriveMode.Output);
            left1.SetDriveMode(GpioPinDriveMode.Output);
            left2.SetDriveMode(GpioPinDriveMode.Output);
            rightE.SetDriveMode(GpioPinDriveMode.Output);
            right1.SetDriveMode(GpioPinDriveMode.Output);
            right2.SetDriveMode(GpioPinDriveMode.Output);
        }

This is the code that makes the car move.  Take note of the different pinValue's.

        private void carForward()
        {
            defaultPins();
            leftE.Write(pinValueHigh);
            rightE.Write(pinValueHigh);
            left1.Write(pinValueLow);
            right1.Write(pinValueLow);
            left2.Write(pinValueHigh);
            right2.Write(pinValueHigh);
        }
        private void carReverse()
        {
            defaultPins();
            leftE.Write(pinValueHigh);
            rightE.Write(pinValueHigh);
            left1.Write(pinValueHigh);
            right1.Write(pinValueHigh);
            left2.Write(pinValueLow);
            right2.Write(pinValueLow);
        }
        private void carRight()
        {
            defaultPins();
            leftE.Write(pinValueHigh);
            rightE.Write(pinValueHigh);
            left1.Write(pinValueLow);
            right1.Write(pinValueHigh);
            left2.Write(pinValueHigh);
            right2.Write(pinValueLow);
        }
        private void carLeft()
        {
            defaultPins();
            leftE.Write(pinValueHigh);
            rightE.Write(pinValueHigh);
            left1.Write(pinValueHigh);
            right1.Write(pinValueLow);
            left2.Write(pinValueLow);
            right2.Write(pinValueHigh);
        }
        private void carStop()
        {
            defaultPins();
        }

This code tells the robot what to do when it detects a Key has been pushed:

        private void Window_KeyDown(CoreWindow sender, KeyEventArgs e)
        {
            Debug.WriteLine(e.VirtualKey);
            if (e.VirtualKey == Windows.System.VirtualKey.Up || e.VirtualKey == Windows.System.VirtualKey.W)
            {
                carForward();
            }

            else if (e.VirtualKey == Windows.System.VirtualKey.Down || e.VirtualKey == Windows.System.VirtualKey.S)
            {
                carReverse();
            }

            else if (e.VirtualKey == Windows.System.VirtualKey.X)
            {
                carStop();
            }

            else if (e.VirtualKey == Windows.System.VirtualKey.Left || e.VirtualKey == Windows.System.VirtualKey.A)
            {
                carLeft();
            }

            else if (e.VirtualKey == Windows.System.VirtualKey.Right || e.VirtualKey == Windows.System.VirtualKey.D)
            {
                carRight();
            }
        }
This code tells the robot to do when it detects the key is no longer pushed:

        private void Window_KeyUp(CoreWindow sender, KeyEventArgs e)
        {
            Debug.WriteLine(e.VirtualKey);
            if (
                e.VirtualKey == Windows.System.VirtualKey.Up || 
                e.VirtualKey == Windows.System.VirtualKey.W ||
                e.VirtualKey == Windows.System.VirtualKey.Down || 
                e.VirtualKey == Windows.System.VirtualKey.S ||
                e.VirtualKey == Windows.System.VirtualKey.Left || 
                e.VirtualKey == Windows.System.VirtualKey.A ||
                e.VirtualKey == Windows.System.VirtualKey.Right || 
                e.VirtualKey == Windows.System.VirtualKey.D ||
                e.VirtualKey == Windows.System.VirtualKey.X
                )
            {
                carStop();
            }
        }
