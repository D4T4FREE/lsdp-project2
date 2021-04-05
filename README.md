# Large Scale Data Processing: Project 2 Report
Sean Chiang and Joshua Yi

## Reporting our findings
1. **(3 points)** Implement the `exact_F2` function. The function accepts an RDD of strings as an input. The output should be exactly `F2 = sum(Fs^2)`, where `Fs` is the number of occurrences of plate `s` and the sum is taken over all plates. This can be achieved in one line using the `map` and `reduceByKey` methods of the RDD class. Run `exact_F2` locally **and** on GCP with 1 driver and 4 machines having 2 x N1 cores. Copy the results to your report. Terminate the program if it runs for longer than 30 minutes.

  - Local
```
==================================
Exact F2. Time elapsed:19s. Estimate: 8567966130
==================================
```
  - GCP
```
==================================
Exact F2. Time elapsed:44s. Estimate: 8567966130
==================================
```

2. **(3 points)** Implement the `Tug_of_War` function. The function accepts an RDD of strings, a parameter `width`, and a parameter `depth` as inputs. It should run `width * depth` Tug-of-War sketches, group the outcomes into groups of size `width`, compute the means of each group, and then return the median of the `depth` means in approximating F2. A 4-universal hash function class `four_universal_Radamacher_hash_function`, which generates a hash function from a 4-universal family, has been provided for you. The generated function `hash(s: String)` will hash a string to 1 or -1, each with a probability of 50%. Once you've implemented the function, set `width` to 10 and `depth` to 3. Run `Tug_of_War` locally **and** on GCP with 1 driver and 4 machines having 2 x N1 cores. Copy the results to your report. Terminate the program if it runs for longer than 30 minutes. **Please note** that the algorithm won't be significantly faster than `exact_F2` since the number of different cars is not large enough for the memory to become a bottleneck. Additionally, computing `width * depth` hash values of the license plate strings requires considerable overhead. That being said, executing with `width = 1` and `depth = 1` should generally still be faster.

  - Local
```
==================================
Tug-of-War F2 Approximation. Width :10. Depth: 3. Time elapsed:107s. Estimate: 5826017569
==================================
```
  - GCP
```
==================================
Tug-of-War F2 Approximation. Width :10. Depth: 3. Time elapsed:502s. Estimate: 3560289317
==================================
```

3. **(3 points)** Implement the `BJKST` function. The function accepts an RDD of strings, a parameter `width`, and a parameter `trials` as inputs. `width` denotes the maximum bucket size of each sketch. The function should run `trials` sketches and return the median of the estimates of the sketches. A template of the `BJKSTSketch` class is also included in the sample code. You are welcome to finish its methods and apply that class or write your own class from scratch. A 2-universal hash function class `hash_function(numBuckets_in: Long)` has also been provided and will hash a string to an integer in the range `[0, numBuckets_in - 1]`. Once you've implemented the function, determine the smallest `width` required in order to achieve an error of +/- 20% on your estimate. Keeping `width` at that value, set `depth` to 5. Run `BJKST` locally **and** on GCP with 1 driver and 4 machines having 2 x N1 cores. Copy the results to your report. Terminate the program if it runs for longer than 30 minutes.

  - Local
```
==================================
BJKST Algorithm. Bucket Size:5787. Trials:5. Time elapsed:25s. Estimate: 5925888.0
==================================
```
  - GCP
```
==================================
BJKST Algorithm. Bucket Size:5787. Trials:5. Time elapsed:62s. Estimate: 5925888.0
==================================
```
  - The number '5787' was derived from the following: Let us denote 'D' as the output of the BJKST algorithm where
    - <img src="https://latex.codecogs.com/png.latex?D=Y_T%202^T" />
    - <img src="https://latex.codecogs.com/png.latex?T=\text%20{%20value%20of%20z%20when%20algorithm%20finishes%20}" />
    - <img src="https://latex.codecogs.com/png.latex?Y_T=\text%20{%20the%20number%20of%20buckets%20}" />
    - <img src="https://latex.codecogs.com/png.latex?d=\text%20{%20the%20exactF0%20value%20}" />
  - Since we want our BJKST algorithm to have an error of +/- 20%, we are to solve the following inequality
    - <img src="https://latex.codecogs.com/png.latex?\lvert(Y_T%202^T%20-%20d)\rvert%3C=0.2*d" />
    - <img src="https://latex.codecogs.com/png.latex?(Y_T%202^T%20-%20d)%3C=0.2*d%20\hspace{2%20mm}\text%20{%20and%20}\hspace{2%20mm}%20(Y_T%202^T%20-%20d)%3E=-0.2*d" />
    - <img src="https://latex.codecogs.com/png.latex?(Y_T%202^T)%3C=1.2*d%20\hspace{2%20mm}\text%20{%20and%20}\hspace{2%20mm}%20(Y_T%202^T)%3E=0.8*d" />
    - <img src="https://latex.codecogs.com/png.latex?Y_T%3C=(1.2*d)/2^T%20\hspace{2%20mm}\text%20{%20and%20}\hspace{2%20mm}%20Y_T%3E=(0.8*d)/2^T" />
  - To find 'd', we run exactF0 on the csv, to which we find '7406649'. To find 'T', we will run our BJKST algorithm on an arbitrary value and solve for 'T' from our above equation, to which we found '10'. We can then substitute in these values to find that
    - <img src="https://latex.codecogs.com/png.latex?5786.4453%3C=Y_T%3C=8679.668" />
  - Therefore, the smallest 'width' required in order to achieve an error of +/- 20% on our estimate will be '5787'


4. **(1 point)** Compare the BJKST algorithm to the exact F0 algorithm and the tug-of-war algorithm to the exact F2 algorithm. Summarize your findings.
```
The BJKST algorithm and F0 algorithm do essentially the same thing where they are outputting the number of distinct counts of the file 2014to2017. The exact F0 algorithm is
essentially counting every value in the file whereas BJKST is taking only a snippet of the stream and finding the median. The ToW algorithm does the same with the F2 algorithm,
in that Tow is a rough estimation of the actual F2 value while the F2 algorithm does the counting directly. Also, the F0 and F2 algorithms seem to have compiled much faster than
the BJKST and ToW algorithm, most likely due to having a faster runtime with in-built functions. 
```
