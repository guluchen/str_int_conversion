Part 1: Getting Started Guide

Introduction

The artifact of paper #869 is a VM exported from VirtualBox as an ova file (Open Virtualization Format). We recommend importing the ova image to VirtualBox with extension pack installed.

Requirements:

Software:
- Please install VirtualBox and VirtualBox extension package. (VMWare also works but some settings may not be correctly imported.)
Hardware:
- The VM is configured to use 4 CPU cores and 4GB RAM of the host system. Configuration can be changed after imported.

Import VM and start:

1. download tgz file and untar to get .ova file
2. import the .ova file to VirtualBox
    a. VM should start with VirtualBox extension package installed
    b. without the Extension Pack, disable USB (Settings->Ports->USB) in the VM
3. when power on, log in with username/password: ubuntu (no GUI)

Run a short test:

Please run the following commands after logged in to test.
$ cd benchmarks            # enter benchmark directory (~/benchmarks)
$ ./ae_run.py test         # test run (will finish in 10 min.)
$ ./ae_clean.sh            # clean up logs
$ tar zxvf ae_logs.tar.gz  # unpack logs previously obtained for report generation test
$ ./ae_gen_summary.sh      # generate summary from logs
$ ./ae_gen_report.py       # generates tables from summary
$ ./ae_clean.sh            # clean up logs

./ae_run.py reports logging information, so it is easy to see if the check is doing well. Note that the tool Z3-PFA in the paper is z3trau in the artifact. If something goes wrong, please check the troubleshooting section below. 


Part 2: Step-by-Step Instructions for how to evaluate the artifact

We propose a full check procedure that is similar to the commands of above test:
$ ./ae_clean.sh         # clean up logs
$ ./ae_run.py           # run benchmark (generate logs, need 5 hrs or more but should be less than 8hrs)
$ ./ae_gen_summary.sh   # generate summary from logs
$ ./ae_gen_report.py    # generate report from summary

The report generated has three tables with the same format as tables in the paper (Section 9, Tables 1, 2, and 3). We have already reduced the case number of two benchmarks (PyEx and full_str_int) to make sure the full check to finish within 8 hrs.

Some details about the artifact:
1. All benchmarks and scripts needed are within ~/benchmark. Source of tools are within home directory and are set to the specific commit (cvc4: 45bcf28a, z3: d95b549ff) mentioned in the paper. Binaries of tools are already installed in /usr/bin or /usr/local/bin.
2. It is important to clean up previous logs (*.log and *.log.err), or the summary/report scripts may go wrong.
3. The procedure should finish within 8 hrs. The actual spent time depends on how fast the host machine is (Our tests took about 5 hrs). If you prefer to check smaller benchmarks, please refer to the instructions below.

How to check smaller benchmark size and some other details about the artifact:
1. Edit the ae_run.py, line 10, the list of benchmarks. ($ nano -c ae_run.py)
2. Edit the benchmark name in the list with the suffix ".n.0" in which n stands for 1/n size. For example, PyEx.8.0 means check 1/8 of PyEx benchmark. You can change it to PyEx.10.0 to check only 1/10 of the benchmark. We recommend changing the size of only PyEx and full_str_int because they are the largest benchmarks (about 20,000 more).
3. To clarify, the script sorts files in a benchmark and pick files from 1-th, n+1-th, 2*n+1-th, ... when set to check 1/n of the benchmark. Therefore all tools check the same files.


Troubleshooting
1. If you encounter incomplete finish of ae_run.py, delete ~/ae.pid before another run.
2. For ubuntu host, if you get a version mismatch error with the package downloading from the VirtualBox site, use "$ apt install virtualbox-ext-pack" should solve.


Part 3: list of claims (with line numbers):

Please note that
1. Z3-PFA = z3trau, and Z3 = z3seq.
2. PyEx in Table 1 and full_str_int in Table 2 (Leetcode and Pythonlib) use only 1/8 of the benchmarks in the artifact evaluation.
3. With a faster host machine, z3seq(Z3) may have better results. In our tests of the VM, z3seq can solve all the checkLuhn (Table 3) problems if provided a fast host. However, z3seq still used more time than z3trau.

Goals of experiments: (line 1047-1055)
1. Z3-PFA (z3trau) performs as good as, or better than other tools in solving the satisfiability problems of basic string constraints. 
2. Z3-PFA (z3trau) performs significantly better than other tools in solving the satisfiability problems on string-number conversion benchmark, and this shows the efficiency of PFA in general and numeric PFA in particular.

Comparisons bases on each table:
Table 1. (line 1143-1154)
From Table1, we can see that the performance of Z3-PFA is as good as the most competitive tools such as CVC4 and Z3 on basic string constraints. In all of the benchmarks, Z3-PFA ranked either the 1st or the 2nd on the number of solved (SAT+UNSAT) cases.

Table 2. (line 1156-1165)
From Table 2, we can see that Z3-PFA significantly outperforms all other tools. The total number of failed cases is only a half to Z3, which is ranked the 2nd.

Table 3. (line 1175-1184)
The result is summarized in Table3. In these tests, Z3-PFA can solve all problems within 1s while CVC4 only returns a model for cases of 2 to 5 loops and Z3-str3 could not solve any of these problems (either timeout or UNKNOWN). However, Z3 can still solve 7 out of the 11 problems and the problems got timeout are cases for 4,5,7, and 9 loops. The behavior of Z3 is not unexpected, since all the problems are satisfiable and sometimes the solver is lucky and went into a branch with a correct model very quickly. 
