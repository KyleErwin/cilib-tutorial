## What is Runner?

`Runner` is an object within CILib that allows us to control the execution of our algorithm.
It provides us with methods that allow us to specify the:

- Number of iterations the algorithm should run for.
- The algorithm.
- The entities in the search space.

Pretty cool, hey?
These are the discussed methods.

```scala
repeat[M[_]: Monad, F[_], A](n: Int, alg: Kleisli[M, F[A], F[A]], collection: RVar[F[A]])
```

"Woah, that looks intimidating".
Sure, it definitely can appear that way so let's try to clarify it a bit.
`n: Int` is the number of iterations the algorithm should run for.
And `collection: RVar[F[B]]` are our *entities* in our search space.
Now the second parameter is something we haven't come across yet.
All it is saying that we need an iteration type based on the algorithm.

### Iteration

`Iteration` is an object defined within `cilib-core` that will create an *
iterator* of our algorithm.
An `Iteration` is an atomic action that applies a given "algorithm"
for each item within the provided `List[A]`.
An `Iteration` may either run synchronously or asynchronously, but not in terms
of concurrency but rather the way changes are applied.
For example, a `synchronous` iterator will apply the algorithm to each
element in the list to create the new collection from iteration to
iteration. An `asynchronous` iterator will update each element in the list
during the iteration.

```scala
sync_[M[_]:Applicative,A,B](f: NonEmptyList[A] => A => M[B]): Kleisli[M,NonEmptyList[A],NonEmptyList[B]]

async_[M[_]: Monad,A](f: NonEmptyList[A] => A => M[A]): Kleisli[M,NonEmptyList[A],NonEmptyList[A]
```

These methods are very *raw* and generic.
They are made public so that if you ever wanted to perhaps make your own iterator you could make use them if you desired so.
The following methods use the *raw* and generic methods to create something we are a bit more familiar with.

```scala
def sync[A, B, C](f: NonEmptyList[B] => B => Step[A, C])

syncS[A, S, B, C](f: NonEmptyList[B] => B => StepS[A, S, C])

 def async[A, B](f: NonEmptyList[B] => B => Step[A, B])

 def asyncS[A, S, B](f: NonEmptyList[B] => B => StepS[A, S, B])
```

We are given methods to handle both `Step` and `StepS` based algorithms.

### foldStep and foldStepS
There is another way for us to execute our algorithms,
`foldStep` and `foldStepS` for `Step` and `StepS` based algorithms respectively.
However, it should be noted that `foldStepS` is not included in
version `2.0.1` and you will need to download the most update branch
of `cilib` to make use of it.
Unlike the `repeat` method the `fold` methods use `scalaz-streams`.
These streams are a great way for composing multiple processes
(in our case algorithms) together.
These methods allow us to specify a couple of things:
- The initial configuration environment
- The RNG
- The collection of initial elements (such `Entity` but could ve anything)
- The algorithm
- A change function that we may use to modify a element that wraps it
 in a `RVar`.
 - An initial state value (only for `foldStepS`)

 Here are the method headers for your interest.

 ```scala
def foldStep[F[_], A, B](initalConfig: Environment[A],
    rng: RNG,
    collection: RVar[F[B]],
    alg: Algorithm[Kleisli[Step[A, ?], F[B], F[B]]],
    env: Process[Task, Problem[A]],
    onChange: F[B] => RVar[F[B]]): Process[Task, Progress[F[B]]]

def foldStepS[F[_], S, A, B](
    initialConfig: Environment[A],
    initialState: S,
    rng: RNG,
    collection: RVar[F[B]],
    alg: Process[Task, Algorithm[Kleisli[StepS[A, S, ?], F[B], F[B]]]],
    env: Process[Task, Problem[A]],
    onChange: (F[B], Eval[NonEmptyList, A]) => RVar[F[B]]):
        Process[Task, Progress[(S, F[B])]]
 ```

### Output
```scala
trait Output
final case class Algorithm[A](name: String Refined NonEmpty, value: A)
final case class Problem[A](name: String Refined NonEmpty,
                            env: Env,
                            eval: RVar[NonEmptyList[A] => Objective[A]])
final case class Progress[A] private (algorithm: String,
                                      problem: String,
                                      seed: Long,
                                      iteration: Int,
                                      env: Env,
                                      value: A)
```


### Conclusion

This may be a lot to take in.
If so don't worry as we are now going to create a `PSO` from start to
finish and see how everything pieces together!
