
# PES-VCS Lab Report

**Name:** Malavika Arun
**SRN:** PES1UG24CS257
**Repository:** https://github.com/malavikaamenon/PES1UG24CS257-pes-vcs

---

## Phase 1: Object Storage Foundation

### Screenshot 1A — `./test_objects` output
<img width="711" height="311" alt="Screenshot 2026-04-21 at 12 00 46 PM" src="https://github.com/user-attachments/assets/f3034e0a-c093-41f0-8cdf-41084baa767b" />

### Screenshot 1B — Sharded directory structure
<img width="752" height="93" alt="Screenshot 2026-04-21 at 12 03 15 PM" src="https://github.com/user-attachments/assets/c3dfc01f-07e6-4401-a9d8-59d18d2930e9" />


---

## Phase 2: Tree Objects

### Screenshot 2A — `./test_tree` output
<img width="439" height="130" alt="Screenshot 2026-04-21 at 12 06 33 PM" src="https://github.com/user-attachments/assets/930ef1b6-8298-4191-9c5d-8fcc6e8bd697" />


### Screenshot 2B — Raw binary format of a tree object
<img width="782" height="378" alt="Screenshot 2026-04-21 at 12 08 56 PM" src="https://github.com/user-attachments/assets/afc8af8c-94e4-4e92-9191-73a37c77410f" />


---

## Phase 3: The Index (Staging Area)

### Screenshot 3A — `pes init` -> `pes add` -> `pes status` sequence
<img width="636" height="364" alt="Screenshot 2026-04-21 at 12 11 42 PM" src="https://github.com/user-attachments/assets/aca85ff8-8609-429c-af91-4f5320fe6f17" />


### Screenshot 3B — Text-format index contents
<img width="786" height="175" alt="Screenshot 2026-04-21 at 12 12 11 PM" src="https://github.com/user-attachments/assets/77c2c380-37b6-4af8-a7a8-fab828f9019b" />


---

## Phase 4: Commits and History

### Screenshot 4A — `./pes log` showing three commits
<img width="498" height="537" alt="Screenshot 2026-04-21 at 12 19 25 PM" src="https://github.com/user-attachments/assets/727f4a38-d98c-4c23-b549-49e048029602" />


### Screenshot 4B — Object store after three commits
<img width="517" height="264" alt="Screenshot 2026-04-21 at 12 20 02 PM" src="https://github.com/user-attachments/assets/ce55ebff-5b8a-45b0-8063-664e992da0c5" />


### Screenshot 4C — Reference chain (HEAD and refs/heads/main)
<img width="683" height="93" alt="Screenshot 2026-04-21 at 6 51 26 PM" src="https://github.com/user-attachments/assets/969aefbb-ada0-4bb8-85ce-889fdb9a14e0" />


---

## Final Integration Test

<img width="242" height="509" alt="Screenshot 2026-04-21 at 6 56 10 PM" src="https://github.com/user-attachments/assets/29fcea96-d5ce-4675-a195-1d4f403c990f" />


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
