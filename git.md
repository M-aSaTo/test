# Rebasing

There are two main ways to integrate changes from one branch into another:

- `merge`
- `rebase`

## The Basic Rebase

<img width="592" alt="screen shot 2016-11-16 at 12 31 45 pm" src="https://cloud.githubusercontent.com/assets/600040/20333822/c1ebc0e6-abf8-11e6-95e6-312fa3939ca1.png">

### The Basic Merge

It performs a three-way merge between the two latest branch snapshots (`C3` and `C4`) and the most recent common ancestor of the two (`C2`), creating a new snapshot (and commit).


<img width="597" alt="screen shot 2016-11-16 at 1 16 18 pm" src="https://cloud.githubusercontent.com/assets/600040/20334623/e53b2a90-abfe-11e6-8f99-845b5e4cbf62.png">

### The Basic Rebase

You can take the patch of the change that was introduced in `C4` and reapply it on top of `C3`.

```
$ git checkout experiment
$ git rebase master
First, rewinding head to replay your work on top of it...
Applying: added staged command
```

<img width="593" alt="screen shot 2016-11-16 at 1 29 14 pm" src="https://cloud.githubusercontent.com/assets/600040/20334827/b523b87a-ac00-11e6-98fd-28c766c84f8b.png">

At this point, you can go back to the `master` branch and do a fast-forward merge.

```
$ git checkout master
$ git merge experiment
```

<img width="579" alt="screen shot 2016-11-16 at 1 42 58 pm" src="https://cloud.githubusercontent.com/assets/600040/20335071/9efdbf44-ac02-11e6-8f72-f1956599a800.png">


Now, the snapshot pointed to by `C4'` is exactly the same as the one that was pointed to by `C5` in the merge example.

There is no difference in the end product of the integration, but the history is different.

## More Interesting Rebases

<img width="583" alt="screen shot 2016-11-16 at 1 59 57 pm" src="https://cloud.githubusercontent.com/assets/600040/20335315/fcbc7c9a-ac04-11e6-805f-08bf76b43b86.png">


### Case

- `server` –  add some server-side functionality to your project, and made a commit.
- `client` –  make the client-side changes and committed a few times.

You want to merge your client-side changes into your mainline for a release, but you want to hold off on the server-side changes until it’s tested further.
