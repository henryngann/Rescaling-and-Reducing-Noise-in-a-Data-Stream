#Rescaling and Reducing Noise in a Data Stream

##Problem

An Arduino board has some sort of sensor that is accepting inputs. These inputs appear to range from 230 to 620. We have 2 problems, the first is that we need data to be between 0 and 255. This is probably to assess the difference between black and white or possibly the absence or presence of light as an object approaches the sensor. In addition the sensor appears to be quite sensitive as number are changing drastically and need to be smoothed out.
##Solution

Keep it simple stupid! There are a lot of ways to smooth out a data stream convolving it with all sorts of filters but we’re going to stick with an average. In addition we can just remap the data from one range to another in order to get the data between 0 and 255.
##Data

![til](https://github.com/henryngann/Rescaling-and-Reducing-Noise-in-a-Data-Stream/blob/main/download.gif)
In this example we will generate some sample data with our above constraints and show an animation of the data. It’s important to note that the animation is doing all of the work, our dataset is static so we already know what the data is we’re just plotting an animation of it.
