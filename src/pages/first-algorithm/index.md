# Our First Algorithm

In this chapter we are going to create our very own PSO algorithm as
well as how to execute the algorithm.
Later in the chapter we will also look at how we can apply our algorithm
to a dynamic environment.
But there are a few things we are going to have to discuss first.

CILib offers a data type that we may use to run our algorithms.
I say "may" because, if so choose, you create your own way of running
the algorithms.
This data type is called `Runner`.
`Runner` isn't a part of the cilib-core, but is included in this part
of the book because it is needed to demonstrate everything we have
learnt.
In order to include `Runner` to your project you need to add `cilib-exec`
as a dependency in your `build.sbt`.

For the purpose of this exercise I don't recommend adding
`cilib-pso` as a dependency purely because
we want to build our algorithm from the ground up.
This way we will gain more experience.

Your library dependencies should look very similar to the following...

```
libraryDependencies ++= Seq(
    "net.cilib" %% "cilib-core" % "@CILIB_VERSION@",
    "net.cilib" %% "cilib-exec" % "@CILIB_VERSION@"
)
```

Once you have refreshed your project we can start looking at what
`Runner` is.

Lastly, it should also be mentioned that we would want to save our
results to some file format.
This is an option coming to the next `cilib` release in the `cilib-io`
package. However, If you wish to check it out now head over to the
`io` section.
