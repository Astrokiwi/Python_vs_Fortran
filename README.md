# Python_vs_Fortran

## To use

You need `gfortran`, `f2py2.7`, and `pythran` installed, as well as Python3, with numpy and scipy. Clone the repo, and go to its directory in your terminal. Then:

```
make
python tests.py
```

C'est tout.

## What/why?

This is an example of a fairly simple calculation that I can't figure out how to do efficiently in Python using the numpy library.
I find with complex algorithms, it often makes sense to code them in a compiled language, but from time to time I find examples of fairly
simple problems that don't appear to have an easy efficient solution in numpy, but have a trivial solution in something like Fortran.
This particularly comes up in O(N^2) type problems, where for some vector of values a_i, I want to calculate a matrix of values
i.e. A_{i,j} = f(a_i,a_j), or something like a vector b_i = sum_j{f(a_i,a_j)}

As an example, here I calculate the potential of a bunch of points, distributed randomly in three dimensions, using a "softened" potential.
This is a fairly simple calculation, but it doesn't appear to be simple to calculate it quickly in Python.

I use a "two loop" naive method in Python, Fortran, Cython, and Pythran, a "one loop" cleverer method in Python, a "magic index" clevererer method in Python, and a `scipy` cleverest method in Python.
By comparison, I also do a direct O(N) sum of all the position coordinates, to show that numpy can actually be just as fast as Fortran if you're
able to let numpy do all the work as intended.

The results I get myself are:

```
```importing and compiling
('N=', 1000)
two_loop_pot         Result=933088.735980 Time=  2.644885
magic_index_pot      Result=933088.735980 Time=  0.045635
one_loop_pot         Result=933088.735980 Time=  0.022956
scipy_pot            Result=933088.735980 Time=  0.003267
cython_pot           Result=933088.735980 Time=  0.002152
naive_pythran_pot    Result=933088.735980 Time=  0.001759
fortran_two_loop_pot Result=933088.735980 Time=  0.001756

numpy_sum            Result=1527.745149 Time=  0.000012
fortran_sum          Result=1527.745149 Time=  0.000006


('N=', 2000)
two_loop_pot         Result=3740364.814098 Time= 11.021113
magic_index_pot      Result=3740364.814098 Time=  0.200300
one_loop_pot         Result=3740364.814098 Time=  0.074092
scipy_pot            Result=3740364.814098 Time=  0.018233
cython_pot           Result=3740364.814098 Time=  0.008139
naive_pythran_pot    Result=3740364.814098 Time=  0.006886
fortran_two_loop_pot Result=3740364.814098 Time=  0.006892

numpy_sum            Result=2995.654231 Time=  0.000016
fortran_sum          Result=2995.654231 Time=  0.000010


('N=', 5000)
two_loop_pot         Result=23521185.786613 Time= 68.735171
magic_index_pot      Result=23521185.786608 Time=  1.282245
one_loop_pot         Result=23521185.786608 Time=  0.365913
scipy_pot            Result=23521185.786608 Time=  0.143840
cython_pot           Result=23521185.786613 Time=  0.050583
naive_pythran_pot    Result=23521185.786613 Time=  0.042717
fortran_two_loop_pot Result=23521185.786613 Time=  0.042645

numpy_sum            Result=7486.093382 Time=  0.000039
fortran_sum          Result=7486.093382 Time=  0.000036
```

## Conclusions

- A big dumb series of nested loops is terrible in pure Python, but works really well in any compiled form. You can use numpy array operations to speed things up quite a lot though, up to maybe 8-9 times slower than the compiled form.

- All of the compiled forms take about the same time, so it doesn't matter if you use Cython, Pythran, or Fortran (it's probably best to stick to whichever one you already know). (Note that Pythran doesn't really support Python3 yet either). (Additional side-note: the specific order of these three depends on your hardware, compiler, optimisation options etc, but they're all generally comparable)

- If you really dig through the libraries, you might find a scipy function that *almost* does what you want anyway, at about 1/3rd the speed of writing out the loop explicitly and compiling it. This gives more compact code, but it took multiple people searching the docs to find the one correct function.

## Appendices:

### Clarification
I am about to post this to Reddit, and so I feel the need to make this clear before somebody "corrects" me: of course the numpy and scipy libraries are compiled as well. I'm really making the comparison between using standard libraries, and writing your own custom loops. The point is that there are fairly simple nested loops that the standard numerical libraries don't cover easily, and even if you do eventually find a library function that is kinda what you want, you might do better to use something compiled like Cython or f2py.

### Why bother with Fortran?
Because Fortran has nice array syntax, and writing heavy numerical work in Fortran is really not all that different form writing it in Python. Many people in the numerical field have experience with Fortran, and then using f2py can come out simpler than trying to figure out Cython. The code ends up almost looking identical anyway - Fortran just has more verbose variable declarations, and index-1 arrays. There is very little difference between Cython code and Fortran. Of course, if you already know Python, there's no point to learn a new language to do something you can already do in Cython.
