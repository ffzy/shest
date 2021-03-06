# Shest

Shest is a Bash based, light-weighted shell script test framework.

# What does Shest do
  - Organizes test cases in two tiers - TC (test case) and TP (test purpose, originated from TETware).
  - Manages test log in a better manner than simply "echo" or "printf".
  - Generates clear test result summary.

# System requirement
  - Linux box with bash shell and any commands required by the test case functions available.

# Run the test
  - "test_suites/\*.sh" is the entry of the test framework. Obviously, simply run one of "test_suites/\*.sh" to kick off the test.
  - Real-time test result summary is printed stderr in console:
```
    [trump@A01-R06-I3-142-H4G9JK2 shest]$ test_suites/shest.sh 
    Test log: /export/users/zhangyue/ws/oss-test/scripts/shest/log/test.log
    --------------------------------------------------------------------------------
    TC tc_oss_simple_test START @ 09/26/18-20:16:08
    TP: tp_host_resolve  s3.cn-east-1.jcloudcs.com ... PASS
    TP: tp_host_resolve_generic_domain_name  s3.cn-east-1.jcloudcs.com ... PASS
    TP: tp_vip_connectivity  100.66.11.130 ... PASS
    TP: tp_put_object  s3.cn-east-1.jcloudcs.com/cn-east-1-simple-test ... PASS
    TP: tp_put_object  cn-east-1-simple-test.s3.cn-east-1.jcloudcs.com ... PASS
    TP: tp_get_object  s3.cn-east-1.jcloudcs.com/cn-east-1-simple-test/hosts ... PASS
    TP: tp_get_object  cn-east-1-simple-test.s3.cn-east-1.jcloudcs.com/hosts ... PASS
    TC tc_oss_simple_test END - 6 of 6 passed
    --------------------------------------------------------------------------------
    TC tc_oss_simple_test START @ 09/26/18-20:16:09
    TP: tp_host_resolve  oss.cn-east-1.jcloudcs.com ... PASS
    TP: tp_host_resolve_generic_domain_name  oss.cn-east-1.jcloudcs.com ... FAIL
    TP: tp_vip_connectivity  222.187.243.43 ... PASS
    TP: tp_vip_connectivity  153.36.228.59 ... PASS
    TP: tp_put_object  oss.cn-east-1.jcloudcs.com/cn-east-1-simple-test ... PASS
    TP: tp_put_object  cn-east-1-simple-test.oss.cn-east-1.jcloudcs.com ... PASS
    TP: tp_get_object  oss.cn-east-1.jcloudcs.com/cn-east-1-simple-test/hosts ... PASS
    TP: tp_get_object  cn-east-1-simple-test.oss.cn-east-1.jcloudcs.com/hosts ... PASS
    TC tc_oss_simple_test END - 1 of 7 failed
    --------------------------------------------------------------------------------
    1 of 13 failed
    Test log: /export/users/zhangyue/ws/oss-test/scripts/shest/log/test.log
```
  - Detailed log is in $shest_home/log/

# Redefine test scope
  - Edit "test_suites/shest.sh" to add more TC's with different test parameters, or remove / comment out the "run_tc" lines to exclude some TC's.
  - Or make a copy of "test_suites/shest.sh" to maintain different test scopes.

# Write test cases
  - Test cases are maintained in "tc/\*.sh" by default. You can create your own test case scripts and source them in test_suites/shest.sh (or any other scripts created based on shest.sh), after sourcing lib/lib_shest.sh, where the test framework functions are maintained.
  - Test cases are organized as described in the chart below:
```
        tc1 param1
          |- tp1
          |- tp2 param1121
          |- tp2 param1122
          |- tp2 param1123
          `- tp3 param113
        tc1 param2
          |- tp1
          |- tp2 param1221
          |- tp2 param1222
          |- tp2 param1223
          `- tp3 param123
        tc2 param1
          |- tp1 param2111
          |- tp1 param2112
          |- tp2 ...
          ....
```
  - TC's are called with "run_tc" function and usually defined in "testcase.sh" or equivalent files:
```
testcase.sh:
    run_tc <tc_function1> <tc_param11> <tc_param12> ...
    run_tc <tc_function1> <tc_param21> <tc_param22> ...
    run_tc <tc_function2> <tc_param11> <tc_param12> ...
    run_tc <tc_function2> <tc_param21> <tc_param22> ...
```
  - TP's are called within TC functions with "run_tp" function:
```
testcase.sh:
    function tc1 {
        typeset param1=${1?}
        typeset param2=${2?}
        typeset tp_param1=$(foo $param1)
        typeset tp_param21=$(bar1 $param2)
        typeset tp_param22=$(bar2 $param2)
        run_tp <tp_function1> $tp_param1
        run_tp <tp_function2> $tp_param1 $tp_param21
        run_tp <tp_function2> $tp_param1 $tp_param22
    }
```
  - Use the log functions provided by the test framework:
```
    log_info "Test step 1 - Open the ice box"                  # Print INFO level log.
    log_eval "ping -c1 epower.zoo.com"                         # Run the command and log the command and its output.
    log_warn "Test step 2 - Put the elephant into the ice box" # Print WARN level log.
    log_error "The ice box explodes"                           # Print ERROR level log.
    log_ "<this is a chart>"                                   # Print the message into log without any logging stamps.
    console_info "TC1 summary: $nfailed / $ntotal failed"      # Print the message into console.
```

