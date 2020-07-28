## Automated Testing

### Overview


**Differnet kinds of automation testing we'll cover:**

1. Unit - Junit, Mockito.
2. Integration - MockMvc, Rest Assured, Awaitility.

- Show my Product project to show how this can look as part of a relatively simple project.

#### Unit Testing

[Junit5](https://junit.org/junit5/)

- Show the tests that I have written.
- Go through a Junit 5 cheat sheet and the different types of assertion - Exception,True,False,Equals etc. Make sure to point out the expected/actual order that trips everyone up. Also go through differences between 4 and 5 that I know. Provide links to more reading material.
- Then do a Kata (Fizz Buzz).
- Teach the TDD approach of little test, little code, passing test, refactor.
- Cover Mockito but very high level and from the point of view of wanting to understand it as a design tool and being able to read it. Rather than knowing all of it - I ceratinly don't ie: Never used spies or PowerMock...  explain why.

#### Integration

- Show tests I have written.
- Run through MockMvc and Rest Assured explaining differences.
- Do some API testing and do some testing round Postgres to show test containers incluing setup and different flavours.
- Explain use of Awaitility - Demonstrate with some async code.
- Show this against my Kube service to prove that it works on any endpoint.



<!--#### BDD 
- Talk about BDD frameworks and show the GTP project
- Write some simple tests.
- Explain that these tests should be written by BA's / The Business - Explain how this doesn't happen and how this mitigates the effectiveness/usefulness/point.

#### Performance
- Show my small gatling project. Make additions/changes to test criteria.
- Write the tests for a new endpoint.
- Show this running against my Kube project.

#### Ian mentioned something about Istio - maybe this would be good to cover in the future.-->


