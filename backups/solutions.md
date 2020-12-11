# Advent of Code 2020

login is **Github** credentials

https://adventofcode.com/2020/


```python
print("hello santa")
```

## Imports Setup Etc


```python
import numpy as np
import pandas as pd
import re
```

## Day 1    

**Part 1**: Find the 2 numbers that add to 2020 report the product of those numbers

**Part 2**: Same as part 1 but with 3 numbers


```python
def day1():

    ## load in data
    with open("input01.txt") as f: # Read file ('with' closes when done)
        info = f.read()
    info = info.split()            # Get list of values (as strings)
    info = [int(s) for s in info]  # Convert strings to ints

    ## Sort data
    info.sort()  #sort the data in place. 

    ### part 1

    ## Check sums on the sorted info
        ## start with smallest number add largest number until you get smaller than 2020 
        ## at that point move to second largest number
    n1 = 0
    n2 = 0
    jj = len(info)-1

    for ii in range(len(info)): 
        while (info[ii] + info[jj]) >= 2020 and n1 == 0:
            if info[ii] + info[jj] == 2020:
                n1 = info[ii]  # grab number we are looking for
                n2 = info[jj]  # grab number
                jj = 1         # break while loop
            jj -= 1
        jj += 1
    
    print("Part 1:")
    print("{} + {} = {}".format(n1,n2,n1+n2))
    print("{} x {} = {}".format(n1,n2,n1*n2))

    ### part 2

    ## similar to part 1, but we'll start one at the lowest, another right above, a third at the end. 
        ## move one at end down until gt 2020 then move second one up and try again (similar to above)
        ## if it's too big at jj==1, then increment ii and start over
    n1 = 0
    n2 = 0
    n3 = 0
    ii = 0
    jj = 1
    kk = len(info)-1
    while n1 == 0:
        while jj < kk and n1 == 0:
            while (info[ii] + info[jj] + info[kk]) >= 2020 and n1 == 0:
    #             print("{} + {} + {} = {}".format(info[ii],info[jj],info[kk],info[ii]+info[jj]+info[kk]))
                if info[ii] + info[jj] + info[kk] == 2020:
                    n1 = info[ii]  # grab number we are looking for
                    n2 = info[jj]  # grab number
                    n3 = info[kk]  # grab number
                kk -= 1
            jj += 1
            kk += 1
        ii += 1
        jj = ii + 1
        kk = len(info)-1

    print("\nPart 2")
    print("{} + {} + {} = {}".format(n1,n2,n3,n1+n2+n3))
    print("{} x {} x {} = {}".format(n1,n2,n3,n1*n2*n3))

day1()
```

    Part 1:
    62 + 1958 = 2020
    62 x 1958 = 121396
    
    Part 2
    117 + 426 + 1477 = 2020
    117 x 426 x 1477 = 73616634


## Day 2

**Part 1**: Count the number of valid passwords. Valid passwords are ones where the number of occurences of the letter indicated are between the min and max number of occurences shown.

**Part 2**: New definition of valid, the letter indicated needs to occur at exactly one of the 2 indicies indicated. Note that this is a "one based" indexing system for the problem.

#### Resources

* StackOverflow XOR in python
  * https://stackoverflow.com/questions/432842/how-do-you-get-the-logical-xor-of-two-variables-in-python
* Documentation for python re library and usage
  * https://docs.python.org/3/library/re.html
* Suuuper Helpful video from Corey Schafer on regular expressions
  * The part that I'm most interested in in about 45 minutes in
  * https://www.youtube.com/watch?v=K8L6KVGG-7o



```python
def day2():
    
    ### Part 1
    
    ## Load in data
    with open("input02.txt") as f: # Read file ('with' closes when done)
        raw = f.read()
    
    ## Split up rows into (min, max, letter, password)
    pat = re.compile("(\d+)-(\d+)\s([a-z]):\s([a-z]+)")
    reres = pat.findall(raw)

    ## Make a list to keep track of valid passwords
    isValid = [False]*len(reres)
    
    ## Check validity
    rcnt = 0
    for row in reres:
        ccnt = 0
        for c in row[3]:
            if c == row[2]:
                ccnt += 1
        isValid[rcnt] = ccnt >= int(row[0]) and ccnt <= int(row[1])
        rcnt += 1
    
    print("Part 1")
    print("There are {} valid passwords".format(sum(isValid)))
    
    ### Part 2
        ## Reusing a lot of part 1. Only validity check changes.
    
    ## Make a list to keep track of valid passwords
    isValid = [False]*len(reres)
    
    ## Check validity
    rcnt = 0
    for row in reres:
        ## see if characters at required indices are valid
            ## the "-1" converts the 1 based idx given to our zero based index
            ## != functions as xor operator comparing booleans
        isValid[rcnt] = ((row[3][int(row[0])-1] == row[2]) != 
                         (row[3][int(row[1])-1] == row[2]))
        rcnt += 1
    
    print("\nPart 2")
    print("There are {} valid passwords".format(sum(isValid)))
            
day2()
```

    Part 1
    There are 536 valid passwords
    
    Part 2
    There are 558 valid passwords


## Day 3

**Part 1**: It's a long description. Essentially count the number of `#` you hit onthe way down moving over 3 cells and down 1 cell. Assume that the pattern given is repeated indefinite number of times horizontally.

This basically looks like it's going to be pretty easy with good use of the modulo function.

**Part 2**: Try again with some different slopes. Report the product of those slopes. Specifically, try:

* Right 1, down 1.
* Right 3, down 1. (This is the slope you already checked.)
* Right 5, down 1.
* Right 7, down 1.
* Right 1, down 2



```python
def day3_help(over = 3, down = 1):
    
    ## It's a little clunky to hardcode the file read but... whatever...
    ## Load in data
    with open("input03.txt") as f: # Read file ('with' closes when done)
        raw = f.read()
    
    ## Split data into a list of rows
    res = raw.split()
    
    ## Work with smaller set initially easier to QC
    # res = res[:10]
    
    ## Set up some control and counter variables
    nrow = len(res)     # variable for number of rows
    ncol = len(res[0])  # variable for num columns (all rows have same)
    colCnt = 0          # track number of collisions
    
    for ii in range(nrow):
        if ii%down == 0 and res[ii][(ii//down)*over%ncol] == "#":
            colCnt += 1
    
    return colCnt

def day3():
    
    ### Part 1
    
    over = 3
    down = 1
    
    colCnt = day3_help(over,down)

    print("Part 1")
    print("You will hit {} trees at an angle of {} to {}".format(colCnt,over,down))
    
    ### Part 2
        ## Reusing a lot of part 1. Only validity check changes.
    
    slopes = [[1,1],
              [3,1],
              [5,1],
              [7,1],
              [1,2]]
    
    cols = [0]*5  # Create empty list to store nubmer of collisions each
    product = 1
    cnt = 0
    for slope in slopes:
        cols[cnt] = day3_help(slope[0],slope[1])
        product *= cols[cnt]
        cnt += 1
    
    print(cols)
    print(np.prod(cols))
    
    print("\nPart 2")
    print("The product of the collisions is {}".format(product))
            
day3()
```

    Part 1
    You will hit 218 trees at an angle of 3 to 1
    [77, 218, 65, 82, 43]
    3847183340
    
    Part 2
    The product of the collisions is 3847183340



```python
print(10 % 7)
print(10/7)
print(10//7)
```

    3
    1.4285714285714286
    1


## Backup

Create alternate backup versions that are easier to diff in git and easier to view in a pinch


```python
#Create a backup of the jupyter notebook in a format for where changes are easier to see.
!jupyter nbconvert solutions.ipynb --to="python" --output="backups/solutions"
!jupyter nbconvert solutions.ipynb --to markdown --output="backups/solutions"

# Also archiving this bad boy
!jupyter nbconvert solutions.ipynb --to html --output="backups/solutions"
```

    [NbConvertApp] Converting notebook solutions.ipynb to python
    [NbConvertApp] Writing 7489 bytes to backups/solutions.py
    Traceback (most recent call last):
      File "/opt/conda/bin/jupyter-nbconvert", line 11, in <module>
        sys.exit(main())
      File "/opt/conda/lib/python3.8/site-packages/jupyter_core/application.py", line 270, in launch_instance
        return super(JupyterApp, cls).launch_instance(argv=argv, **kwargs)
      File "/opt/conda/lib/python3.8/site-packages/traitlets/config/application.py", line 845, in launch_instance
        app.start()
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 350, in start
        self.convert_notebooks()
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 524, in convert_notebooks
        self.convert_single_notebook(notebook_filename)
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 490, in convert_single_notebook
        write_results = self.write_single_notebook(output, resources)
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 450, in write_single_notebook
        write_results = self.writer.write(
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/writers/files.py", line 126, in write
        with io.open(dest, 'w', encoding='utf-8') as f:
    FileNotFoundError: [Errno 2] No such file or directory: 'backups/solutions.py'
    [NbConvertApp] Converting notebook solutions.ipynb to markdown
    [NbConvertApp] Writing 12124 bytes to backups/solutions.md
    Traceback (most recent call last):
      File "/opt/conda/bin/jupyter-nbconvert", line 11, in <module>
        sys.exit(main())
      File "/opt/conda/lib/python3.8/site-packages/jupyter_core/application.py", line 270, in launch_instance
        return super(JupyterApp, cls).launch_instance(argv=argv, **kwargs)
      File "/opt/conda/lib/python3.8/site-packages/traitlets/config/application.py", line 845, in launch_instance
        app.start()
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 350, in start
        self.convert_notebooks()
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 524, in convert_notebooks
        self.convert_single_notebook(notebook_filename)
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 490, in convert_single_notebook
        write_results = self.write_single_notebook(output, resources)
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 450, in write_single_notebook
        write_results = self.writer.write(
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/writers/files.py", line 126, in write
        with io.open(dest, 'w', encoding='utf-8') as f:
    FileNotFoundError: [Errno 2] No such file or directory: 'backups/solutions.md'
    [NbConvertApp] Converting notebook solutions.ipynb to html
    [NbConvertApp] Writing 605579 bytes to backups/solutions.html
    Traceback (most recent call last):
      File "/opt/conda/bin/jupyter-nbconvert", line 11, in <module>
        sys.exit(main())
      File "/opt/conda/lib/python3.8/site-packages/jupyter_core/application.py", line 270, in launch_instance
        return super(JupyterApp, cls).launch_instance(argv=argv, **kwargs)
      File "/opt/conda/lib/python3.8/site-packages/traitlets/config/application.py", line 845, in launch_instance
        app.start()
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 350, in start
        self.convert_notebooks()
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 524, in convert_notebooks
        self.convert_single_notebook(notebook_filename)
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 490, in convert_single_notebook
        write_results = self.write_single_notebook(output, resources)
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/nbconvertapp.py", line 450, in write_single_notebook
        write_results = self.writer.write(
      File "/opt/conda/lib/python3.8/site-packages/nbconvert/writers/files.py", line 126, in write
        with io.open(dest, 'w', encoding='utf-8') as f:
    FileNotFoundError: [Errno 2] No such file or directory: 'backups/solutions.html'



```python

```
