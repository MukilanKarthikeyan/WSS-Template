# Visualizing the Fourier Series
## Introduction
The goal of the project is to generate Visualizations for how the Fourier series creates approximation of curves. The end result is a function that is able to generate two differnt graphics to show using Circles to represent the trignometric waves used in the Fourier Series.The Fourier Series in Calculus is the summation of trignometric waves in order to represent an approximation of a curve. Since the Fourier Series uses trignometirc functions which are created by the rotation of a circle, it is possible to link together multiple rotating circles and recreate the curve.
## Development
First,I had to learn Calculus and trignometry in order to understand how the Fourier Series works. At first I wanted a proof of concept that I was able to visualize how the addition of sinatudial could result in an approximation of the curve. So I spent some time to hard code rotating circles. I created rotating circles by setting the centers of successive circles as a sintudial functions so that they would be following a track built by the previous circle's circumfrence. 
  
  
  	Animate[
 	   Graphics[
  		{
   			Line[{{0, 0}, {5*Cos[time], 5*Sin[time]}}],
 			Circle[{0, 0}, 5],
   
  			Circle[{5*Cos[time], 5*Sin[time]}, 3],
  			Line[{{5*Cos[time], 5*Sin[time]}, {3*Cos[2 time] + 5*Cos[time], 3*Sin[2 time] + 5*Sin[time]}}],
   			
			Circle[{3*Cos[2 time] + 5*Cos[time], 3*Sin[2 time] + 5*Sin[time]},2],
  			Line[{{3*Cos[2 time] + 5*Cos[time], 3*Sin[2 time] + 5*Sin[time]}, {5*Cos[time] + 3*Cos[2 time] + 2 Cos[3 time], 5*Sin[time] + 3*Sin[2 time] + 2 Sin[3 time]}}],
  			 }
 		 ],
 	{time, 0, 2*Pi}, AnimationRunning -> False]
