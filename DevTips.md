# Dev Tips

## Git
	
When using Git.

- Do pull with rebase

- Check 3rd answer of this to see why we rebase instead of just pulling: [Pull with Re-base](https://stackoverflow.com/questions/18930527/difference-between-git-pull-and-git-pull-rebase) 

For this it is important to understand the difference between Merge and Rebase.

Rebases are how changes should pass from the top of hierarchy downwards and merges are how they flow back upwards.

- Squash commits 

- [With Command Line](https://github.com/wprig/wprig/wiki/How-to-squash-commits) 
- [Sourctree: Squash Commits](https://community.atlassian.com/t5/Sourcetree-questions/In-SourceTree-how-do-I-squash-commits/qaq-p/345666)
- [Sourcetree: Interactive Rebase](https://www.atlassian.com/blog/sourcetree/interactive-rebase-sourcetree)


## Container Set Up's

##### When de-bugging Kafka we need to run the console consumer **before** making any Curl requests that we expect to get through - Othrerwise we will see 0 messagaes being consumed. 

[How Kafka  Works](https://rmoff.net/2018/08/02/kafka-listeners-explained/)


## New Libraries~

[Metrics](https://metrics.dropwizard.io/4.0.0/getting-started.html)

[Hawtio: monitoring tool](https://hawt.io/docs/)

[ToxiProxy]
(https://medium.com/@ravindraprasad/testing-known-unknowns-using-toxiproxy-75dfc9d0dc1)