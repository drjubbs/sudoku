---
jupyter:
  jupytext:
    formats: ipynb,md
    text_representation:
      extension: .md
      format_name: markdown
      format_version: '1.2'
      jupytext_version: 1.8.0
  kernelspec:
    display_name: Python 3
    language: python
    name: python3
---

```python
import numpy as np
import itertools
```

```python
def print_puzzle(puzzle):
    print(" +---------+---------+---------+")
    for i in range(9):
        print(" | ", end=' ')
        for j in range(9):
            if puzzle[i,j]>0:
                print("{}".format(puzzle[i,j]), end=' ')
            else:
                print(".", end=' ')
            if j % 3 == 2:
                print(" | ", end=' ')
        print()
        if i % 3 == 2:
            print(" +---------+---------+---------+")

```

```python
def subcell_index(a,b):
    """For subsquare a,b in range 0, 1, 2 return
    indices which corresponds to all elements in that
    square.
    """
    ii=[3*a+t for t in range(3)]
    jj=[3*a+t for t in range(3)]
    return(itertools.product(ii,jj))

def subcell(this_i, this_j):
    """For cell (i, j) return a list of indices for other locations inside the subcell.
    For example, cell 0, 0 is in the upper left subsquare, so return 0 0, 0 1, 0 2 .. 2 0, 2 1, 2 2
    """
    ilist=[]
    jlist=[]
    for i in range(9):
        for j in range(9):
            if (i // 3 == this_i // 3) and (j // 3 == this_j // 3):
                ilist.append(i)
                jlist.append(j)
                
    return(ilist, jlist)

print(subcell(1,1)==([0, 0, 0, 1, 1, 1, 2, 2, 2], [0, 1, 2, 0, 1, 2, 0, 1, 2]))
print(subcell(8,8)==([6, 6, 6, 7, 7, 7, 8, 8, 8], [6, 7, 8, 6, 7, 8, 6, 7, 8]))
print(subcell(3,3)==([3, 3, 3, 4, 4, 4, 5, 5, 5], [3, 4, 5, 3, 4, 5, 3, 4, 5]))
```

```python
def find_legal_squares(puzzle, digit):
    """For a given digit, return a list of legal squares."""
    
    # Start by assuming everywhere is legal
    legal_squares=np.ones([9,9]).astype(int)

    # Occupied squares are not legal
    for i in range(9):
        for j in range(9):
            if puzzle[i,j]>0:
                legal_squares[i,j]=0
    
    # Rows and columns
    for i in range(9):
        for j in range(9):
            if puzzle[i,j]==digit:
                legal_squares[i,:]=0
                legal_squares[:,j]=0
                
    # Subsquares
    for i in range(9):
        for j in range(9):
            if puzzle[i,j]==digit:
                ilist, jlist = subcell(i,j)
                for i2, j2 in zip(ilist, jlist):
                    legal_squares[i2,j2]=0
                    
    return(legal_squares)
    
```

```python
#def main():

puzzle=np.array([
[0, 0, 5, 6, 0, 7, 4, 0, 0],
[4, 8, 0, 0, 0, 0, 0, 3, 5],
[0, 0, 0, 0, 5, 0, 0, 0, 0],
[0, 1, 0, 2, 0, 6, 0, 9, 0],
[0, 7, 0, 3, 0, 5, 0, 8, 0],
[0, 5, 0, 9, 0, 1, 0, 6, 0],
[0, 0, 0, 0, 6, 0, 0, 0, 0],
[8, 6, 0, 0, 0, 0, 0, 5, 7],
[0, 0, 1, 7, 0, 8, 2, 0, 0]])


print_puzzle(puzzle)

counter=1
solved = False
while counter<10:

    # 3D Matrix of all legal moves
    allowed_digits=np.zeros([9,9,9])
    for t in range(9):
        allowed_digits[:,:,t]=find_legal_squares(puzzle, t+1)
    allowed_digits=allowed_digits.astype(int)

    # Where only one digit is legal, fill in the answer
    possible_solutions=np.sum(allowed_digits,axis=2)
    for i in range(9):
        for j in range(9):
            if possible_solutions[i,j]==1:
                for z in range(9):
                    if allowed_digits[i,j,z]==1:
                        puzzle[i,j]=z+1

    # Loop over subsquares and look for positions where
    # a digit only has one possability, these need to get filled in
    for a in range(3):
        for b in range(3):
            for d in range(9):
                sub_puzzle=puzzle[a*3:(a+1)*3,b*3:(b+1)*3]
                sub_allowed=allowed_digits[a*3:(a+1)*3,b*3:(b+1)*3 ,d]
                if np.count_nonzero(sub_allowed)==1:
                    sub_puzzle[sub_allowed==1]=(d+1)
                
                                
    print("===> {}".format(np.count_nonzero(puzzle)))
    print_puzzle(puzzle)
    counter=counter+1

#if __name__ == "__main__":
#    main()
```

```python

```
