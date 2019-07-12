# Visualizing the Fourier Series
## Introduction
The goal of the project is to generate Visualizations for how the Fourier series creates approximation of curves. The end result is a function that is able to generate two differnt graphics to show using Circles to represent the trignometric waves used in the Fourier Series.The Fourier Series in Calculus is the summation of trignometric waves in order to represent an approximation of a curve. Since the Fourier Series uses trignometirc functions which are created by the rotation of a circle, it is possible to link together multiple rotating circles and recreate the curve.
## Development
### Understanding the General Idea
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
I went through many itterations of my code an restarted on a new approach three times. The hardest part of this project was getting the information form the functions to be in a certain format so that it could be displayed in a cohesive manner. This issue rose form creating a generalized funtion that would work for most if not all senarios.
#### Approach One 

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

### Approach Two
#### Why I Took a Second Approach and How I did it
Althought the previous method works, it is hard to know exactly if you are creating the desired fucntion,Which means that you need to know what the radii/amplititues and frequency where before you used this function. Hence my second approach. This approach was more of a function that would be built as an addition to the previous method. The new addition included a function built around the inbuilt function "FourierSeries" that took in funciton, variable, and number of terms as inputs and returned the equation of the approximation of the function as complex numbers.I had to use the inbuild function "ComplexExpand" to get the equation in terms of Sine and Cosine. If the input function was even, then the Sine terms would cancel, and if the fucntion is odd, then the Cosine terms cancled out.
#### How the Code Works

So this function was labled FourierInputV2 because this was my second itteration to this fuction which took in a function, the variable that is being using, and the number of terms desired. 

	fourierInputV2[inputFunction_,variableUsing_,numberOfTerms_Integer]:=
	With[
		{protoSeries=FourierSeries[inputFunction,variableUsing,numberOfTerms]//ComplexExpand,
		manipulateSeries=Drop[Apply[List,FourierSeries[inputFunction,variableUsing,numberOfTerms]//ComplexExpand],1],
		protoCoordinateSeries={
			{
	Select[Drop[Apply[List,FourierSeries[inputFunction,variableUsing,numberOfTerms]//ComplexExpand],1],!FreeQ[#,Cos]&],
					Select[Drop[Apply[List,FourierSeries[inputFunction,variableUsing,numberOfTerms]//ComplexExpand],1],!FreeQ[#,Cos]&]/.Cos->Sin
				},
				{
					Select[Drop[Apply[List,FourierSeries[inputFunction,variableUsing,numberOfTerms]//ComplexExpand],1],!FreeQ[#,Sin]&],
					Select[Drop[Apply[List,FourierSeries[inputFunction,variableUsing,numberOfTerms]//ComplexExpand],1],!FreeQ[#,Sin]&]/.Sin->Cos
				}
			}	
		},

			Join[
				MapThread[List,{
					Select[manipulateSeries,!FreeQ[#,Cos]&],
					Select[manipulateSeries,!FreeQ[#,Cos]&]/.Cos->Sin
				}],
		
				MapThread[List,
				{
					Select[manipulateSeries,!FreeQ[#,Sin]&],
					Select[manipulateSeries,!FreeQ[#,Sin]&]/.Sin->Cos
				}
			]
		]
]


### Final Approach And Why it worked
#### Creating a New Visual
For my final approach, I used a pair of funcitons called "FourierCosCoefficient" and "FourierSinCoefficient" in order seprately get the radii of the circles. Then the many varibles are used to artificially stich togeather the coeffiecients and the frequencies; the code is using a descriete Fourier Transform, thus allowing me to artificially generate the frequiences used. Then the artificailly generated fourier series is used to generate coordinates. In the begining I had the x coordinates to be in terms of Cosine and y coordinates in terms of Sine which caused the roataing parts to move in the counter clockwise direction. This function then creates individual moving plots which are then combined with the circles through the "Show" function to give the illusion that the circles are creating the plots. This function is named "unblendedCosCircleSmoothie" because this function returns the sperate waves that were added to create the approximation, hence the "ingriedients" in the "smoothie." I created this funciton as a demonstariton to show how seprate waves and circles can create function when combined toagather; the different steps of the process would be a good visual aid in understaing the concept of what the Fourier Series does. Below there is an example of the funciton that is used for the even functions. For the odd funcitons I replaced a few operations and swiched wherever it Cos to Sine and vise-versa. 

	ClearAll[unblendedCosCircleSmoothie]

	unblendedCosCircleSmoothie[function_,variable_Symbol,numCir_Integer]:=
	Block[
		{
			fourierCoeff=Table[FourierCosCoefficient[function,variable,p],{p,1,numCir}],
			color=Drop[Hue[#]&/@Range[0,1,(1/numCir)],-1],
			circleList,basicCoord,manipulatedCoord,maxRadius,lineList,plotList,styledLines,prePlot
		},
		circleList=Circle[{-maxRadius-5,0},#1]&/@Abs@fourierCoeff;
		
		basicCoord=MapThread[Times,{fourierCoeff,Cos[Range[numCir]*variable]}];
		manipulatedCoord=Transpose[{basicCoord-maxRadius-5,basicCoord/.Cos->Sin}];
		maxRadius=Max[Abs@fourierCoeff];
		lineList=Line[{{-maxRadius-5,0},#1,{0,#1[[2]]}}]&/@manipulatedCoord/.variable->$time;
		styledLines=MapThread[Style,{lineList,color}];
		
		prePlot=MapThread[Times,{fourierCoeff,Cos[(Range[numCir]*(variable+$time))-(Pi/2)]}];
		plotList=Plot[#1,{variable,0,10},PlotStyle->#2]&@@@(Transpose[{prePlot,color}]);
		
		Animate[
			Show[
				Graphics[{Sequence@@#1,Sequence@@#2},Axes->True],
				Plot[#3,{variable,0,10},PlotStyle->#4,PlotRange->All],PlotRange->All
				],
		{$time,0,-2Pi},AnimationRunning->False]&@@Evaluate[{circleList,styledLines,prePlot,color}]

#### Creating the Nested Visual
The following funciton is structred similar to the previous algorithim. However, the difference is that now the centers of the circles are not static and are changed so that they create a chain. There is an algorithim that takes the set of coordinates and nests them together so that the circles follow an orbit. Another difference in this function is that instead of creaing differnt rotating arms form the origin in order to color them, I was able to insert the entire nested funciton list into the line wich created a line that appeared to be segmented. 

	ClearAll[circleCosSmoothie2]
	circleCosSmoothie2[function_,variable_Symbol,numCir_Integer]:=
		Block[{
			fourierConstant=FourierCosCoefficient[function,variable,0]//ComplexExpand,
			fourierCoeff=Table[FourierCosCoefficient[function,variable,p],{p,1,numCir}],
			basicCoord,manipulatedCoord,lastYCoord,totalRadius,combinedCir,cirCenter,
			cirArguments,finalPlot,lineSeries,centerOfFirstCir,listOfCoord,lastLinetoYaxis,
			lastXCoord,plotCoord,plotAllign
	
	
	},
	
	totalRadius=Total[Abs@fourierCoeff];
	basicCoord=MapThread[Times,{fourierCoeff,Cos[Range[numCir]*$time]}];
	manipulatedCoord=Join[
		Take[
			Transpose[
			{(basicCoord-totalRadius-5)/.Cos->Sin,basicCoord}],1],
		Drop[Transpose[{basicCoord/.Cos->Sin,basicCoord}],1]];
	manipulatedCoord=(Plus@@@Table[
				manipulatedCoord[[;;n]],
					{n,1,Length[manipulatedCoord]}]//Prepend[{-totalRadius-5,0}]);
								
	lastYCoord={0,Take[Take[manipulatedCoord,-1]//Flatten,-1]};
	plotCoord=Total[MapThread[Times,{fourierCoeff,Cos[((Range[numCir])*(variable+$time))]}]];
	cirCenter=Drop[manipulatedCoord,-1];
	cirArguments=Transpose[{cirCenter,Abs@fourierCoeff}];
	
	lastLinetoYaxis=Prepend[Take[Take[manipulatedCoord,-1]//Flatten,-1],0];
	
	combinedCir={Circle[#1,#2]}&@@@(#1&/@cirArguments);
	lineSeries= Line[#1]&@(Append[manipulatedCoord,lastLinetoYaxis]);

	Animate[
			Show[
				Graphics[{Sequence@@#1,#2},Axes->True],
				Plot[#3,{variable,0,3*#4},PlotRange->Full],
				PlotRange->{{-3*#4,3#4},{-#4,#4}}
								],
		{$time,0,-2Pi},AnimationRunning->False]&@@Evaluate[{combinedCir,lineSeries,plotCoord,totalRadius}]
	
	
	]


#### Putting it All Together

	
	ClearAll[fourierCircleCookBook]
	fourierCircleCookBook[function_,variable_Symbol,numCir_Integer]:=
	Which[
		PossibleZeroQ[function-(function/.variable->(-variable))],
			Row[
				{unblendedCosCircleSmoothie[function,variable,numCir],
				circleCosSmoothie2[function,variable,numCir],
				Plot[function,{variable,-30,30},PlotStyle->Black,Frame->True,ImageSize->Medium,Axes->False]}
			],
		PossibleZeroQ[function+(function/.variable->(-variable))],
			Row[
				{unblendedSinCircleSmoothie[function,variable,numCir],
				circleSinSmoothie2[function,variable,numCir],
				Plot[function,{variable,-30,30},PlotStyle->Black,Frame->True,ImageSize->Medium,Axes->False]}
			],
	True,
	circleSmoothietheRest[function,variable,numCir]
	]
## Further Improvements
In the future, when I have more time, I hope to get the function to create a visualization for any function even if it is neither odd nor even. By doing so I will be able to draw any curve that is provided using rotating arm segments. The end goal is to create a function, such that if given any picture of a curve, it will recreate the cruve using a series of circle linkages. Finally, I will cloud deploy the funcitons so that there will be a mini site that is easy for users to acess and utilize. 


[1]: 
