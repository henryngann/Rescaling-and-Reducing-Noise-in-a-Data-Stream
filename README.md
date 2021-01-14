#Rescaling and Reducing Noise in a Data Stream 

##Problem

An Arduino board has some sort of sensor that is accepting inputs. These inputs appear to range from 230 to 620. We have 2 problems, the first is that we need data to be between 0 and 255. This is probably to assess the difference between black and white or possibly the absence or presence of light as an object approaches the sensor. In addition the sensor appears to be quite sensitive as number are changing drastically and need to be smoothed out.
##Solution

Keep it simple stupid! There are a lot of ways to smooth out a data stream convolving it with all sorts of filters but we’re going to stick with an average. In addition we can just remap the data from one range to another in order to get the data between 0 and 255.
##Data

![til](https://github.com/henryngann/Rescaling-and-Reducing-Noise-in-a-Data-Stream/blob/main/download.gif)
In this example we will generate some sample data with our above constraints and show an animation of the data. It’s important to note that the animation is doing all of the work, our dataset is static so we already know what the data is we’re just plotting an animation of it.

##Scaling
This data is not in the scale that we want it to be in so first let’s pass in our scaling function to rescale the data. Again the data is stale we are using the animation to simulate a data stream. The red line shows the original unscaled data and the blue line displays the new scale. It’s important to note that if the MIN or MAX values of the original dataset change, the resulting scaled data will be larger or smaller than the intended scale. To prevent this the function will take any point below the min or above the max and remap it to the min or the max set by the new range.
![til](https://raw.githubusercontent.com/hashrocket/hr-til/master/app/assets/images/banner.png)
Smoothing
We now have rescaled data but it looks like crap. The input is too “jumpy” and we want a better transition from state to state of the data input. You could spend an entire career looking for “filters” to convolve with your original dataset, but keeping with our mantra of KISS, we’re going to use an average.

Warnings
A few important rules, the more data points we average the less actual data we get out. Also the more points we average, the more we “squish” the data, this may have unintended consequences especially if our boundaries or maxes and mins denote some sort of terminal state. Really it depends on the sensor, so if we were building some sort of automated flight module for a drone and it reports data at 100 Hz (100 times a second) if we smooth the data for every 500 data points it’s not going to evaluate data until it has 5 seconds (500 data points at 100 data points a second is 5 seconds). Depending on how fast the drone is moving this could be critical, we don’t want the thing to smash into a wall because the average is 500 data points. In addition, remember we are averaging data so all the data points are weighted equally. If the drone drifted towards an object and the last data point it hits the object and the first data point the sensor doesn’t even see the object, the average is going to be somewhere in the middle between hitting the object and not even seeing it. So be careful with your averages use a number that makes the data smooth enough while still keeping features of the original dataset intact. Too much smoothing will result in a flat line, too little smoothing will result in following the “jumpiness” of the data too closely. Be the baby bear, where things are just right.

Here is a plot of the final dataset, I am showing both a 10-point smoother and a 4-point smoother so you can see how the different amount of points used produces different results. The original data is still red and blue, the 4 point smoother is green and the 10 point smoother is orange. Notice that the less points you use to average, the closer it follows the original dataset, the more points you use the more the data just tends to get “squished” towards the overall average of the entire dataset.

![til](https://raw.githubusercontent.com/hashrocket/hr-til/master/app/assets/images/banner.png)

I/O & Averaging
Streaming Data
It’s important to note that this data is static, only the plots simulate “streaming data”. In a real situation with streams of data you need to wait (n - 1) data points before you can generate a smoothing set. For example if you have a smoother that uses 10 points you will get 9 points of NA, Nulls or 0s depending on what you decide to pad the data with. It’s because you’re averaging the first 10 points so you don’t get a number until you have 10 data points to start with. Then you want to “roll” the averaging process through your data by 1 index each time. This is called convolution.

Averaging & Linear Algebra
An average is typically denoted as a sum of data over the count of data points. You can break this concept down into linear algebra if you want brownie points. If you have a set of data, x, and a filter (our averaging process is a filter), y, and the two are the same length you can use a “dot product” to get your smoothed data.

Example
Our 10-point smoother is just an average of 10 points if we break down the average process into 2 pieces, we are summing over the data and then dividing. But division is just an inverted multiplication, so by dividing by 10 we are actually multiplying by 1/10 (0.1). So if we have a set of data, x, of length 10, it needs to be in a row vector or matrix with just 1 row. Then we use our other vector, y, of length 10, who’s values are all 0.1 and it needs to be a column vector or matrix with just 1 column. If we use matrix multiplication and sum it up we create a dot product, which will generate our average. For more on dot products see this website (https://mathinsight.org/dot_product_examples).

Generated Datasets
So you can check your work if you’re using another language like Python or C, I’ve included all of the data and generated data in an object known as a dataframe. Think of the dataframe like an excel file.
