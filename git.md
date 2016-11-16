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

You can take the changes on client that aren’t on server (`C8` and `C9`) and replay them on your master branch by using the `--onto` option of git rebase:

```
$ git rebase --onto master server client
```

This basically says, “Check out the client branch, figure out the patches from the common ancestor of the `client` and `server` branches, and then replay them onto master.”

After that, you can fast-forward your master branch:

```
$ git checkout master
$ git merge client
```

<img width="591" alt="screen shot 2016-11-16 at 2 24 10 pm" src="https://cloud.githubusercontent.com/assets/600040/20335739/6338223c-ac08-11e6-964e-07cacf445011.png">

You can rebase the `server` branch onto the `master` branch without having to check it out first by running `git rebase [basebranch] [topicbranch]`:

```
$ git rebase master server
```

It's means:

```
$ git checkout server
$ git rebase master
```

<img width="578" alt="screen shot 2016-11-16 at 2 32 35 pm" src="https://cloud.githubusercontent.com/assets/600040/20335906/aa85cf58-ac09-11e6-8035-21784d9a2690.png">

Then, you can fast-forward the `master` branch:

```
$ git checkout master
$ git merge server
```

and remove the `client` and `server` branch:

```
$ git branch -d client
$ git branch -d server
```

<img width="580" alt="screen shot 2016-11-16 at 2 35 16 pm" src="https://cloud.githubusercontent.com/assets/600040/20335951/ecc316d2-ac09-11e6-9f75-e42217871e30.png">

## The Perils of Rebasing

**Do not rebase commits that exist outside your repository.**

<img width="590" alt="screen shot 2016-11-16 at 2 46 41 pm" src="https://cloud.githubusercontent.com/assets/600040/20336177/83bcc118-ac0b-11e6-84f7-7e0a805468bc.png">

Someone else does more work that includes a merge, and pushes that work to the central server. You fetch it and merge the new remote branch into your work, making your history look something like this:

<img width="579" alt="screen shot 2016-11-16 at 2 47 44 pm" src="https://cloud.githubusercontent.com/assets/600040/20336211/b10bd708-ac0b-11e6-878d-bb2ff8213ba3.png">

Next, the person who pushed the merged work decides to go back and rebase their work instead; they do a `git push --force` to overwrite the history on the server. You then fetch from that server, bringing down the new commits.

<img width="576" alt="screen shot 2016-11-16 at 2 53 21 pm" src="https://cloud.githubusercontent.com/assets/600040/20336304/735ee142-ac0c-11e6-8944-3bab48e36eef.png">

If you do a `git pull`, you’ll create a merge commit which includes both lines of history:

<img width="576" alt="screen shot 2016-11-16 at 2 55 07 pm" src="https://cloud.githubusercontent.com/assets/600040/20336338/b2c220b0-ac0c-11e6-8b7b-aa1551c15b20.png">

If you run a `git log`, you’ll see two commits that have the same author, date, and message, which will be confusing. Furthermore, if you push this history back up to the server, which can further confuse people.

## Rebase When You Rebase

In addition to the commit SHA-1 checksum, Git also calculates a checksum that is based just on the patch introduced with the commit. This is called a “patch-id”. 

If you pull down work that was rewritten and rebase it on top of the new commits from your partner, Git can often successfully figure out what is uniquely yours and apply them back on top of the new branch.

For example,

<img width="570" alt="screen shot 2016-11-16 at 3 21 49 pm" src="https://cloud.githubusercontent.com/assets/600040/20336816/6bf3cc7a-ac10-11e6-9b03-58cf35b13c58.png">

- Determine what work is unique to our branch (`C2`, `C3`, `C4`, `C6`, `C7`)
- Determine which are not merge commits (`C2`, `C3`, `C4`)
- Determine which have not been rewritten into the target branch (just `C2` and `C3`, since `C4` is the same patch as `C4'`)
- Apply those commits to the top of `teamone/master`

<img width="584" alt="screen shot 2016-11-16 at 3 09 27 pm" src="https://cloud.githubusercontent.com/assets/600040/20336589/b2c8eba0-ac0e-11e6-9448-ca80db9ed5fe.png">

This only works if C4 and C4' that your partner made are almost exactly the same patch.

You can do it by `git pull` with the `--rebase` option:

```
git pull --rebase
```

or 


```
git fetch
git rebase teamone/master
```

If you are using `git pull` and want to make `--rebase` the default, you can set the pull.rebase config value with something like `git config --global pull.rebase true`.


## Rebase vs. Merge

Which one is better "Rebase" or "Merge"? It depends on what history means for you.

History means:

**Record of what actually happened**

Merge is better.

**Story of how your project was made**

Rebase is better.
