# Visualizing the Fourier Series
## Introduction
The goal of the project is to generate Visualizations for how the Fourier series creates approximation of curves. The end result is a function that is able to generate two differnt graphics to show using Circles to represent the trignometric waves used in the Fourier Series.The Fourier Series in Calculus is the summation of trignometric waves in order to represent an approximation of a curve. Since the Fourier Series uses trignometirc functions which are created by the rotation of a circle, it is possible to link together multiple rotating circles and recreate the curve.
## Development
###Understanding the General Idea
First,I had to learn Calculus and trignometry in order to understand how the Fourier Series works. At first I wanted a proof of concept that I was able to visualize how the addition of sinatudial could result in an approximation of the curve. So I spent some time to hard code rotating radii of circles and orbiting circles. I created orbiting circles by setting the centers of successive circles as a sintudial functions so that they would be following a track built by the previous circle's circumfrence. 
  
  
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
![What the code Creates][1]

The lines are the roating arms and connect one center of a circle to the next circle's center. The code above shows the nesting of coordinates to create the moving parts using the "Animate" function. If the x-coordinates use "Cosine" and the y-coordinates use "Sine" functions, then the roatating parts will move counter-clockwise.If you switch the functions, then the arms rotate clockwise. (This information proves to be useful later on in the developing process)


### Creating the Functions
I went through many itterations of my code an restarted on a new approach three times. 
#### Approach One and Two

At first I tried to create multiple helper functions to manipulate the information and create it modular. The code below takes in a radius list, frequency list, and the number of circles needes, and then creates the nested list that is used for the moving objects in the graphics. The function is then called multiple times (I at that time had not optimimized by code to run quickly) and used them to create list of Graphics Primitieves. This one of the more developed versions of this method where the urser physically inputs the amplitude and radii of the waves used to generate the desirded funtion. In earilier versions I had an issue with getting animate to work; when I was not using slots in order to input lists and other information. I later found out that the function "Animate" had a "HoldAll" attribute which meant that it would not evaluate anything that is inputed directly in the Animate. Therefore, I had to adapt my code to evaluate outside the Animate and then be inserted into the Animate function via Slot Machines. 

	ClearAll[fourierSeriesGraphicsV3]
	fourierSeriesCoordinates[numberCircles_,radiusList_List,frequencyList_List]:=
		With[
			{protoList=MapThread[
					{#1*Cos[#2*time],#1*Sin[#2*time]}&,{radiusList,frequencyList}]
					},
		Plus@@@Table[protoList[[;;n]],
		{n,1,Length[protoList]}]//Prepend[{0,0}]
	]

	fourierSeriesGraphicsV3[numberCircles_,radiusList_List,frequencyList_List]:=
		With[
		{
			coordinateList=fourierSeriesCoordinates[numberCircles,radiusList,frequencyList],
			protoList=fourierSeriesCoordinates[numberCircles,radiusList,frequencyList],
			lastCoordinate=Drop[Apply[List,
				MapThread[
					Circle,{
						Drop[
							fourierSeriesCoordinates[10,Reverse[Range[10]],Range[10]],-1],
							Reverse[Range[10]]}
						]//Last],-1][[1,2]],
			circleList=Evaluate[
					MapThread[
						Circle,{
							Drop[fourierSeriesCoordinates[numberCircles,radiusList,frequencyList],-1],
							radiusList}
						]
					]
		},

	Animate[
		Graphics[{#1,#2,Line[Last[coordinateList],{Total[radiusList]+10,lastCoordinate}]}],
	{time,0,2*Pi},
	AnimationRunning->False]&@Evaluate[
		(Line[coordinateList]),(circleList)
	]
	]


My Second 

#### Final Approach And Why it worked


## Further Improvements
In the future, when I have more time, I hope to get the function to create a visualization for any function even if it is neither odd nor even. By doing so I will be able to draw any curve that is provided using rotating arm segments. The end goal is to create a function, such that if given any picture of a curve, it will recreate the cruve using a series of circle linkages. Finally, I will cloud deploy the funcitons so that there will be a mini site that is easy for users to acess and utilize. 


[1]: 
