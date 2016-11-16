# Rebasing

There are two main ways to integrate changes from one branch into another:

- `merge`
- `rebase`

## The Basic Rebase

### The Basic Merge

<img width="592" alt="screen shot 2016-11-16 at 12 31 45 pm" src="https://cloud.githubusercontent.com/assets/600040/20333822/c1ebc0e6-abf8-11e6-95e6-312fa3939ca1.png">

It performs a three-way merge between the two latest branch snapshots (`C3` and `C4`) and the most recent common ancestor of the two (`C2`), creating a new snapshot (and commit).


<img width="597" alt="screen shot 2016-11-16 at 1 16 18 pm" src="https://cloud.githubusercontent.com/assets/600040/20334623/e53b2a90-abfe-11e6-8f99-845b5e4cbf62.png">

### The Basic Rebase

```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

You can take the patch of the change that was introduced in `C4` and reapply it on top of `C3`.

<img width="593" alt="screen shot 2016-11-16 at 1 29 14 pm" src="https://cloud.githubusercontent.com/assets/600040/20334827/b523b87a-ac00-11e6-98fd-28c766c84f8b.png">


```
$ git checkout master
$ git merge experiment
```
