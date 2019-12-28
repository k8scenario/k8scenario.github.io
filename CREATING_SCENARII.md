
# Instructions for creating new scenarii for the k8scenario tool

A scenario is a zip file, to be downloaded by the k8scenario tool.

The source for scenarii is in the https://github.com/k8scenario/k8scenario directory, under the SCENARII folder.

An example is "scenario0" which is a basic "hello world" type of scenario which is typically demonstrated to participants at the beginning of the workshop.

These are the *source* files for scenario0, as you might expect *EXCLUDE_SOLUTION.txt* is excluded from the scenario zip.  This file is a description of the scenario solution.

```bash
> ls -altr SCENARII/scenario0/
total 4
-rw-rw-rw- 1 mjb mjb    0 Sep 17 11:18 9.empty.txt
-rw-rw-rw- 1 mjb mjb  545 Sep 19 16:30 functions.rc
-rw-rw-rw- 1 mjb mjb  172 Sep 19 16:48 EXCLUDE_SOLUTION.txt
-rwxr-xr-x 1 mjb mjb   51 Oct 30 09:15 check.sh
-rwxr-xr-x 1 mjb mjb  204 Oct 30 09:41 INSTRUCTIONS.txt
```

The above folder is comprised of several files:
- 9.empty.txt
- functions.rc
- EXCLUDE_SOLUTION.txt
- check.sh
- INSTRUCTIONS.txt
<!-- </p> -->

</details>



# Instructions for testing new scenarii for the k8scenario tool

<details><summary>show</summary>
<!-- <p> -->
TODO ...
<!-- </p> -->
</details>


## TODO:
<details><summary>show</summary>
<!-- <p> -->

- Create scenario YAML manifests (and optionally bash scripts) to
  - Check cluster functionality - OK with regards scenario
  - Install scenario
  - Check cluster functionality - broken
  - Uninstall scenario
  - Check cluster functionality - OK with regards scenario
<!-- </p> -->
</details>


<!-- <p> -->
<!-- </p> -->

