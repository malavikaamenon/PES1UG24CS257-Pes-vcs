# PES-VCS Lab Report

**Name:** Malavika Arun
**SRN:** PES1UG24CS257
**Repository:** https://github.com/malavikaamenon/PES1UG24CS257-pes-vcs

---

## Phase 1: Object Storage Foundation

### Screenshot 1A — `./test_objects` output
![1A](screenshots/1A_test_objects_output.png)

### Screenshot 1B — Sharded directory structure
![1B](screenshots/1B_objects_sharded.png)

---

## Phase 2: Tree Objects

### Screenshot 2A — `./test_tree` output
![2A](screenshots/2A_test_tree_output.png)

### Screenshot 2B — Raw binary format of a tree object
![2B](screenshots/2B_raw_xxd_tree.png)

---

## Phase 3: The Index (Staging Area)

### Screenshot 3A — `pes init` -> `pes add` -> `pes status` sequence
![3A](screenshots/3A_init_add_status.png)

### Screenshot 3B — Text-format index contents
![3B](screenshots/3B_index_contents.png)

---

## Phase 4: Commits and History

### Screenshot 4A — `./pes log` showing three commits
![4A](screenshots/4A_pes_log.png)

### Screenshot 4B — Object store after three commits
![4B](screenshots/4B_objects_after_commits.png)

### Screenshot 4C — Reference chain (HEAD and refs/heads/main)
![4C](screenshots/4C_refs_and_head.png)

---

## Final Integration Test

![Integration](screenshots/Final_integration_test.png)

All integration tests completed successfully.

---

# Analysis Questions

## Q5.1 — Implementing `pes checkout <branch>`

To implement pes checkout <branch>, update .pes/HEAD to point to the branch and read the commit hash from .pes/refs/heads/<branch>. Then load the commit’s tree and reconstruct the working directory to match that snapshot. This involves creating, modifying, and deleting files accordingly. The operation is complex because it must avoid overwriting uncommitted changes and handle recursive directory structures safely.
## Q5.2 — Detecting a dirty working directory using only the index and object store

To detect a dirty working directory, compare each indexed file’s metadata (mtime, size) with the current working directory. If different, recompute the file’s hash and compare it with the stored index hash. If they differ and the file also differs in the target branch, a conflict exists. In such cases, checkout must abort to prevent data loss.
## Q5.3 — Detached HEAD

In a detached HEAD state, HEAD points directly to a commit hash instead of a branch. New commits can still be created, but no branch references them, making them unreachable later. These commits can be recovered by creating a new branch pointing to their hash. Otherwise, they may be removed during garbage collection.
## Q6.1 — Finding and deleting unreachable objects

Garbage collection starts from all branch references and traverses commits, trees, and blobs to mark reachable objects. A hash set is used to efficiently track visited objects. After traversal, any object not in the reachable set is deleted. For large repositories, this may involve visiting hundreds of thousands of objects.
## Q6.2 — Race condition between GC and concurrent commits

Running GC during a commit is dangerous because newly created objects may not yet be referenced. GC might incorrectly delete these objects, leading to repository corruption. This creates a race condition between writing objects and marking them reachable. Git avoids this using locks, temporary references, and delaying deletion of recent objects.
