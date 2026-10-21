# Git Merge Strategies 

Git မှာ **merge** ဆိုတာ branch တစ်ခုရဲ့ changes တွေကို နောက် branch တစ်ခုထဲ ပေါင်းထည့်တာ ဖြစ်ပါတယ်။ အောက်မှာ သုံးလေ့ရှိတဲ့ merge ပုံစံတွေကို ရှင်းပြထားပါတယ်။


## 1. Fast-Forward Merge (FF)

`main` ပေါ်မှာ commit အသစ်မရှိဘဲ `feature` ကပဲ ရှေ့ဆက်သွားရင် `main` pointer ကို feature ရဲ့ နောက်ဆုံး commit ဆီ ရွှေ့ပေးနိုင်ပါတယ်။ **Merge commit အသစ် မဖန်တီးပါ။**

```text
Before:
A ── B ── C (main)
           \
            D ── E (feature)

After:
A ── B ── C ── D ── E (main, feature)
```


## 2. Three-Way / Merge Commit

`main` နဲ့ `feature` နှစ်ဖက်စလုံးမှာ commit အသစ်တွေ ရှိနေတဲ့အခါ Git က **common ancestor** ကို အခြေခံပြီး changes တွေ ပေါင်းပါတယ်။ ပုံမှန်အားဖြင့် merge commit `M` အသစ် ပေါ်လာပါတယ်။ Conflict ရှိရင် ဖြေရှင်းရပါတယ်။

```text
Before:
           D ── E (feature)
          /
A ── B ── C ── F (main)

After:
           D ── E ──┐
          /          │
A ── B ── C ── F ─── M (main)
```


## 3. Non-Fast-Forward Merge (`--no-ff`)

FF လုပ်လို့ရသော်လည်း **merge commit တစ်ခု မဖြစ်မနေ ဖန်တီး** ချင်ရင် သုံးပါတယ်။ Feature ကို branch အလိုက် history ထဲမှာ သိသာစေပါတယ်။

```bash
git switch main
git merge --no-ff feature
```

```text
Before:
A ── B ── C (main)
           \
            D ── E (feature)

After:
A ── B ── C ─────── M (main)
           \         /
            D ── E ─┘ (feature)
```

## 4. Squash Merge (`--squash`)

Feature branch ရဲ့ changes အားလုံးကို **commit အသစ်တစ်ခုအဖြစ် စုပေါင်း** ပါတယ်။ ပုံမှန် merge commit မဖန်တီးဘဲ changes တွေကို staging area ထဲ ပြင်ဆင်ပေးတာကြောင့် ကိုယ်တိုင် commit လုပ်ရပါတယ်။

```text
Before:
A ── B ── C ── F (main)
           \
            D ── E (feature)

After:
A ── B ── C ── F ── G (main)
           \
            D ── E (feature)

G = D နှင့် E မှ changes တွေကို ပေါင်းထားသော commit အသစ်
```



**သတိ:** Squash merge က feature branch ရဲ့ commit history ကို `main` ထဲ တစ်ခုချင်းမယူပါ။ Merge ancestry ကိုလည်း ပုံမှန် merge လို မမှတ်သားပါ။


# Git Branching & Stash 

**Branch** ဆိုတာ project history ရဲ့ လမ်းကြောင်းတစ်ခုကို ပြတဲ့ pointer ဖြစ်ပါတယ်။ Branch တစ်ခုစီမှာ သီးခြားအလုပ်လုပ်ပြီး နောက်မှ ပေါင်းနိုင်ပါတယ်။

## Branch ဖန်တီးခြင်းနှင့် ပြောင်းခြင်း

```bash
git branch dev          # dev branch ဖန်တီးမည် (မပြောင်းသေး)
git switch dev          # dev branch သို့ ပြောင်းမည်
git checkout dev        # အဟောင်း syntax ဖြင့် ပြောင်းမည်

git switch -c uat       # uat ကို ဖန်တီးပြီး တစ်ခါတည်း ပြောင်းမည်
git checkout -b uat     # အဟောင်း syntax
```

## Branch ကြည့်ခြင်း၊ အမည်ပြောင်းခြင်း

```bash
git branch                # Local branch များ ကြည့်မည်
git branch -a             # Local + remote-tracking branch များ ကြည့်မည်
git branch --show-current # လက်ရှိ branch နာမည် ကြည့်မည်
git branch -m new-name    # လက်ရှိ branch ကို အမည်ပြောင်းမည်
```

## Branch ဖျက်ခြင်း

```bash
git branch -d uat             # Merge ဖြစ်ပြီးဟု Git စစ်ဆေးနိုင်မှ ဖျက်မည်
git branch -D uat             # မပေါင်းရသေးလည်း local branch ကို force-delete လုပ်မည်
git push origin --delete uat  # Remote origin ပေါ်ရှိ uat branch ကို ဖျက်မည်
```

- လက်ရှိ ရောက်နေတဲ့ branch ကို ဖျက်လို့မရပါ။ အခြား branch တစ်ခုသို့ အရင်ပြောင်းပါ။ `main` ကိုပဲ ပြောင်းဖို့ မလိုပါ။
- `-D` သုံးလျှင် merge မလုပ်ရသေးသည့် commits များ ဆုံးရှုံးနိုင်သဖြင့် သတိထားပါ။
- Remote branch ကို ဖျက်တာနဲ့ local branch ကို ဖျက်တာ **မတူပါ**။

## Git Stash

**Stash** က မပြီးသေးတဲ့ local changes တွေကို commit မလုပ်ဘဲ ခဏသိမ်းပေးပါတယ်။ အဲဒီနောက် working tree ကို ရှင်းပြီး branch ပြောင်းနိုင်ပါတယ်။

```bash
git stash                         # Tracked changes များကို ခဏသိမ်းမည်
git stash -u                      # Untracked files ပါ သိမ်းမည်
git stash --include-untracked     # -u နှင့် အတူတူ
git stash list                    # Stash ID များ ကြည့်မည်
git stash show -p 'stash@{0}'      # သတ်မှတ် stash ရဲ့ code changes ကြည့်မည်

git stash apply                   # နောက်ဆုံး stash ကို ပြန်ယူမည် (stash မဖျက်)
git stash apply 'stash@{0}'       # သတ်မှတ် stash ကို ပြန်ယူမည်
git stash pop                     # နောက်ဆုံး stash ကို ပြန်ယူပြီး အောင်မြင်လျှင် ဖျက်မည်
git stash drop 'stash@{0}'        # သတ်မှတ် stash တစ်ခု ဖျက်မည်
git stash clear                   # Stash အားလုံး ဖျက်မည် (သတိ)
```

# Git Time Travel 

Git ရဲ့ **Time Travel** ဆိုတာ အရင် commit ကို ပြန်ကြည့်ခြင်း၊ အပြောင်းအလဲကို ပြန်လှန်ခြင်း၊ သို့မဟုတ် branch pointer ကို အရင် commit ဆီ ရွှေ့ခြင်း ဖြစ်ပါတယ်။

## 1. အရင် commit ကို ပြန်ကြည့်ခြင်း (Detached HEAD)

```text
A ── B ── C ── D (main)
     ↑
  B ကို စစ်ကြည့်ရန်
```

```bash
git checkout <commit-hash>         # အရင် commit ကို checkout လုပ်မည်
git switch --detach <commit-hash> # Modern detached HEAD syntax

git switch main                  # main ၏ လက်ရှိနောက်ဆုံး commit ဆီ ပြန်မည်
# git checkout main             # အဟောင်း syntax
```

**မှတ်ရန်:** အရင် commit ကို ကြည့်တာဟာ နောက်ပိုင်း commits တွေကို ဖျက်တာ မဟုတ်ပါ။ 

## 2. Git Revert — Commit ကို ပြန်လှန်ခြင်း

အရင် commit ရဲ့ changes ကို ပြန်လှန်တဲ့ **commit အသစ်** တစ်ခု ဖန်တီးပါတယ်။ Original history မပျောက်ပါ။

```text
Before: A ── B ── C ── D
After:  A ── B ── C ── D ── E
                            ↑
                     E သည် C ကို revert
```

```bash
git revert <commit-hash> # အဲဒီ commit ရဲ့ changes ကို ပြန်လှန်သော commit အသစ်ဖန်တီးမည်
```

**အသုံးပြုရန်:** Shared/remote history ကို မပြောင်းဘဲ bug ဖြစ်တဲ့ commit ကို undo လုပ်ချင်သောအခါ။

## 3. Git Reset — Branch ကို အရင် commit ဆီ ရွှေ့ခြင်း

```text
Before: A ── B ── C ── D (main)
After:  A ── B ── C (main)
```


```bash
git reset --soft <commit>  # HEAD ကို ရွှေ့၊ changes ကို staged ထားမည်
git reset --mixed <commit> # HEAD ကို ရွှေ့၊ changes ကို unstaged ထားမည်
git reset --hard <commit>  # HEAD၊ staging နှင့် tracked files ကို reset လုပ်မည်
```

- `--mixed` က default ဖြစ်လို့ `git reset <commit>` နဲ့ အတူတူပါ။
- `--hard` သည် untracked files အားလုံးကို အလိုအလျောက် မဖျက်ပါ။
- Remote ကို ပို့ပြီးသား history ကို reset ပြောင်းပြီး push လုပ်လျှင် ပုံမှန် push ပယ်ခံရနိုင်ပါတယ်။ History overwrite လိုအပ်မှသာ `git push --force-with-lease` ကို အထူးသတိနဲ့ သုံးပါ။

**အဓိကကွာခြားချက်:** `revert` က undo commit အသစ်ထည့်တယ်။ `reset` က branch pointer ကို ရွှေ့တယ်။


# Git Rebase vs Merge 

`merge` နဲ့ `rebase` နှစ်ခုစလုံးက branch တစ်ခုရဲ့ changes တွေကို နောက် branch နဲ့ ပေါင်းစပ်ရန် သုံးပါတယ်။ **History ပုံစံက မတူပါ။**

## Git Merge

`merge` က branch histories နှစ်ခုကို ပေါင်းပါတယ်။ Branch တွေ ခွဲထွက်ထားလျှင် merge commit အသစ် ဖန်တီးနိုင်ပါတယ်။ Original commits တွေကို မပြောင်းပါ။

```text
Before:
A ── B ── C ── F (main)
           \
            D ── E (feature)

After merge:
A ── B ── C ── F ─── M (main)
           \           /
            D ── E ───┘ (feature)
```

```bash
git switch main
git merge feature     # feature changes ကို main ထဲ ပေါင်းမည်
```

## Git Rebase

`rebase` က feature ရဲ့ commits တွေကို `main` ရဲ့ နောက်ဆုံး commit ပေါ် **ပြန်တင်ပြီး commit အသစ်များအဖြစ် ပြန်ဖန်တီး** ပါတယ်။ Commit hashes ပြောင်းပါတယ်။

```text
Before:
A ── B ── C ── F (main)
           \
            D ── E (feature)

After rebase:
A ── B ── C ── F (main)
                \
                 D' ── E' (feature)
```

```bash
git switch feature   # feature branch သို့ ပြောင်းမည်
git rebase main       # feature commits တွေကို main ပေါ် ပြန်တင်မည်
```

**သတိ:** Rebase ပြီးတာနဲ့ `main` pointer က မရွှေ့သေးပါ။ `main` ထဲ ပေါင်းချင်လျှင် ထပ် merge လုပ်ပါ။

```bash
git switch main
git merge feature    # main မတိုးသေးလျှင် fast-forward ဖြစ်နိုင်သည်
```

```text
Final:
A ── B ── C ── F ── D' ── E' (main, feature)
```

## Rebase Conflict ဖြစ်လျှင်

```bash
git status              # Conflict ရှိသည့် files ကို စစ်မည်
# Files တွေကို edit လုပ်ပြီး conflict ဖြေရှင်းပါ
git add <file>          # ဖြေရှင်းထားသော file ကို stage လုပ်မည်
git rebase --continue   # Rebase ဆက်လုပ်မည်
git rebase --abort      # Rebase ကို ဖျက်သိမ်းပြီး မူလအခြေအနေပြန်သွားမည်
```






