Download Link: https://assignmentchef.com/product/solved-lab-6-cmput-398
<br>
<h1>Objective</h1>

The lab’s objective is to implement a simple image convolution and tiled image convolution using both shared and constant memory. We will have a constant 5×5 convolution mask, but will have arbitrarily sized image (assume the image dimensions are greater than 5×5 for this Lab).

For the simple image convolution, you shouldn’t use any constant or shared memory. Keep it simple. It should look very similar to the pseudo code shown below. However, for the tiled image convolution you need to use both shared and constant memory.

To use the constant memory for the convolution mask, you can first transfer the mask data to the device. Consider the case where the pointer to the device array for the mask is named M. You can use const float * __restrict__ M as one of the parameters during your kernel launch. This informs the compiler that the contents of the mask array are constants and will only be accessed through pointer variable M. This will enable the compiler to place the data into constant memory and allow the SM hardware to aggressively cache the mask data at runtime.

Convolution is used in many fields, such as image processing for image filtering. A standard image convolution formula for a 5×5 convolution filter M with an Image I is:

<em>P</em><em>i, j,c</em><em>M </em><em>x ,y</em>

<em>x</em>=−2 <em>y</em>=−2

where <em>P</em><em><sub>i, j,c </sub>P</em><em><sub>i, j,c</sub></em> is the output pixel at position i,j in channel c, <em>I</em><em><sub>i, j,c</sub></em> is the input pixel at i,j in channel c (the number of channels will always be 3 for this MP corresponding to the RGB values), and <em>M</em><em><sub>x , y</sub></em> is the mask at position x,y.

This lab will be submitted as one zipped file through <a href="https://eclass.srv.ualberta.ca/portal/">eclass</a><a href="https://eclass.srv.ualberta.ca/portal/">.</a> Details for submission are at the end of the lab.

<h1>Input Data</h1>

The input is an interleaved image of height x width x channels. By interleaved, we mean that the the element I[y][x] contains three values representing the RGB channels. This means that to index a particular element’s value, you will have to do something like:

index = (yIndex*width + xIndex)*channels + channelIndex;

For this assignment, the channel index is 0 for R, 1 for G, and 2 for B. So, to access the G value of I[y][x], you should use the linearized expression I[(yIndex*width+xIndex)*channels + 1].

For simplicity, you can assume that channels is always set to 3.

<h1>Instructions</h1>

Edit the code where the TODOs are specified and perform the following:

<ul>

 <li>Allocate device memory</li>

 <li>Copy host memory to device</li>

 <li>Initialize thread block and kernel grid dimensions</li>

 <li>Invoke CUDA kernel</li>

 <li>Copy results from device to host</li>

 <li>Deallocate device memory</li>

 <li>Implement the simple 2D convolution kernel with adjustments for channel</li>

 <li>Implement the tiled 2D convolution kernel with adjustments for channels</li>

 <li>Use shared memory to reduce the number of global accesses, handle the boundary conditions in when loading input list elements into the shared</li>

</ul>

memory

<h1>Pseudo Code</h1>

A sequential pseudo code would look something like this:

maskWidth := 5

maskRadius := maskWidth/2 <em># this is integer division, so the result is 2 </em><strong>for</strong> i from 0 to height <strong>do </strong>  <strong>for</strong> j from 0 to width <strong>do </strong>    <strong>for</strong> k from 0 to channels       accum := 0

<strong>for</strong> y from -maskRadius to maskRadius <strong>do</strong>

<strong>for</strong> x from -maskRadius to maskRadius <strong>do </strong>          xOffset := j + x           yOffset := i + y

<strong>if</strong> xOffset &gt;= 0 &amp;&amp; xOffset &lt; width &amp;&amp;              yOffset &gt;= 0 &amp;&amp; yOffset &lt; height <strong>then</strong>

imagePixel := I[(yOffset * width + xOffset) * channels + k]             maskValue := K[(y+maskRadius)*maskWidth+x+maskRadius]             accum += imagePixel * maskValue           <strong>end </strong>        <strong>end </strong>      <strong>end</strong>

<em># pixels are in the range of 0 to 1</em>

P[(i * width + j)*channels + k] = clamp(accum, 0, 1)     <strong>end </strong>  <strong>end end</strong>

where clamp is defined as

<strong>def</strong> clamp(x, lower, upper)

<strong>return</strong> min(max(x, lower), upper) <strong>end</strong>

<h1>Local Setup Instructions</h1>

Steps:

<ol>

 <li>Download “Lab6.zip”.</li>

 <li>Unzip the file.</li>

 <li>Open the Visual Studios Solution in Visual Studios 2013.</li>

 <li>Build the project. Note the project has two configurations.

  <ol>

   <li>Debug</li>

   <li>Submission</li>

  </ol></li>

</ol>

But make sure you have the “Submission” configuration selected when you finally submit.

<ol start="5">

 <li>Run the program by pressing the following button:</li>

</ol>

Make sure the “Debug” configuration is selected. Running the program in Visual Studios will run one of the tests located in “Dataset/Test”.

<h1>Testing</h1>

To run all tests located in “Dataset/Test”, first build the project with the “Submission” configuration selected. Make sure you see the “Submission” folder and the folder contains the executable.

To run the tests, click on “Testing_Script.bat”. This will take a couple of seconds to run and the terminal should close when finished. The output is saved in “Marks.js”, but to view the calculated grade open “Grade.html” in a browser. If you make changes and rerun the tests, then make sure you reload “Grade.html”. You can double check with the timestamp at the top of the page.

You will be given two executable files for reference on simple 2D convolution and tiled convolution (in the director “References”). <strong>Do NOT submit these files.</strong> On each version, your code run time on the <strong>Submission configuration</strong> on <strong>Test 7 </strong>should be at most 3 times slower than the reference file of that version to get mark.

Write a report with screenshots show the run time of your code compare to the reference file of each version. <strong>If you don’t submit this report, you will lose 20% of you mark.</strong>


