# PES-VCS Lab Report

**Author:** KRASSVEEN ROBERT  
**SRN:** PES2UG24CS639

---

## Phase 1: Object Storage Foundation

### Screenshot 1A: Output of `./test_objects`
![1A - test_objects](images/Screenshot%202026-04-22%20000143.png)
<img width="818" height="310" alt="Screenshot 2026-04-22 000143" src="https://github.com/user-attachments/assets/1cb0055b-5400-43f8-aaab-bd0056d04e45" />


### Screenshot 1B: Sharded directory structure
![1B - sharded objects](images/Screenshot%202026-04-22%20001542.png)

---<img width="919" height="602" alt="Screenshot 2026-04-22 001542" src="https://github.com/user-attachments/assets/d992905f-380a-4088-a566-3eab210e7e62" />


## Phase 2: Tree Objects

### Screenshot 2A: Output of `./test_tree`
![2A - test_tree](images/Screenshot%202026-04-22%20001542.png)
<img width="919" height="602" alt="Screenshot 2026-04-22 001542" src="https://github.com/user-attachments/assets/e747f254-cf0a-404b-8ab1-e4d1bf234514" />

### Screenshot 2B: Hex dump of a raw tree object
![2B - tree hexdump](images/Screenshot%202026-04-22%20001604.png)

---<img width="1269" height="395" alt="Screenshot 2026-04-22 001604" src="https://github.com/user-attachments/assets/32c60991-f6a0-4a18-b587-38e289b2d868" />


## Phase 3: The Index (Staging Area)

### Screenshot 3A: Staging area status
![3A - staging status](images/Screenshot%202026-04-22%20001640.png)
<img width="967" height="767" alt="Screenshot 2026-04-22 001640" src="https://github.com/user-attachments/assets/22bd7aa4-7c2c-4c05-bd80-9344e3c5482b" />


### Screenshot 3B: Raw text index format
![3B - index file](images/Screenshot%202026-04-22%20001659.png)

---

## Phase 4: Commits and History

### Screenshot 4A: Commit history log
![4A - pes log](images/Screenshot%202026-04-22%20001659.png)
<img width="1054" height="775" alt="Screenshot 2026-04-22 001659" src="https://github.com/user-attachments/assets/9bd1992e-9129-4249-92f1-bd6686e85fcb" />


### Screenshot 4B: Object store growth
![4B - objects growth](images/Screenshot%202026-04-22%20001712.png)
<img width="1054" height="775" alt="Screenshot 2026-04-22 001659" src="https://github.com/user-attachments/assets/907fdbf9-adca-4387-b439-c879d1d3edea" />


### Screenshot 4C: Reference chain (HEAD and main)
![4C - refs](images/Screenshot%202026-04-22%20001712.png)
<img width="922" height="466" alt="Screenshot 2026-04-22 001712" src="https://github.com/user-attachments/assets/e08469fb-50f4-4014-9dda-93a15fc343df" />


### Final Screenshot: Full integration test
![Final - integration test](images/Screenshot%202026-04-22%20001807.png)
<img width="565" height="742" alt="Screenshot 2026-04-22 001807" src="https://github.com/user-attachments/assets/8f08d962-abb0-4cf9-a566-1eee227a2f8a" />

---

## Phase 5: Branching and Checkout (Analysis)

### Q5.1: Implementing Checkout
Implementing `pes checkout <branch>` requires updating `.pes/HEAD` to point to the branch reference and then replacing the working directory files with the blobs from the target commit's tree. The complexity lies in safely overwriting working directory files without destroying uncommitted changes.

### Q5.2: Dirty Working Directory Conflict
A conflict occurs when a tracked file has unstaged changes and the target branch contains a different version of that file. The system must compare the file's mtime/size with the index, and if the blob hashes differ between the current and target commits, checkout must abort.

### Q5.3: Detached HEAD
Detached HEAD means `.pes/HEAD` contains a raw commit hash instead of a branch reference. Commits made in this state are not reachable from any branch after switching away. They can be recovered by creating a new branch at that commit hash.

---

## Phase 6: Garbage Collection and Space (Analysis)

### Q6.1: Garbage Collection Algorithm
PES-VCS uses a mark-and-sweep algorithm: traverse all reachable objects from branch tips and HEAD, mark them in a hash set, then delete any object in `.pes/objects/` not in the set. For 100,000 commits and 50 branches, the algorithm visits roughly 100,000 commit objects plus all reachable trees and blobs.

### Q6.2: GC Race Condition
If GC runs between `pes add` and `pes commit`, it may delete the newly added blob because it is not yet referenced by any commit or tree. Git avoids this by using a grace period—only deleting unreferenced objects older than a certain time (e.g., two weeks).
