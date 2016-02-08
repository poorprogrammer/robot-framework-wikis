Introduction

These are high-level guidelines for writing test cases. How to actually interact with the system under test is out of the scope of this document.
Most important guideline is keeping test cases as easy to understand as possible for people familiar with the domain.
See also related Dos and Don'ts slides.
For more information about this subject, you might want to take a look at these great resources:
Writing Maintainable Automated Acceptance Tests article by Dale H. Emery.
How to Structure a Scalable And Maintainable Acceptance Test Suite blog post by Andreas Ebbert-Karroum.
Naming

Test suite names

Suite names should be as describing as possible.
Names can be relatively long, but over 40 characters starts to be too much.
Remember that suite names are created automatically from file/directory names. Extensions are stripped, underscores are converted to spaces and, if name is all lower case, words are capitalized. For example login_tests.txt -> Login Tests and DHCP_and_DNS -> DHCP and DNS.
Test case names

Test names should be describing similarly as suite names.
If a suite contains many similar tests, and the suite itself is well named, test names can be shorter.
Name is exactly what you you specify in the test case file without any conversion.
invalid_login_should_fail.txtinvalid_login_should_fail.txt
*** Test Cases ***

Login With Empty Password Should Fail

Login With Empty Username Should Fail

Login With Empty Username And Password Should Fail

Login With Invalid Username Should Fail

Login With Invalid Password Should Fail

Login With Invalid Username And Invalid Password Should Fail

*** Test Cases ***

Empty Password

Empty Username

Empty Username And Password

Invalid Username

Invalid Password

Invalid Username And Invalid Password

Keyword names

Also keyword names should be describing and clear.
Should explain what the keyword does, not how it does it.
Very different abstraction levels (e.g. Input Text or User logs into system).
*** Keywords ***

Input Valid Username And Valid Password And Click Login Button

*** Keywords ***

Login With Valid Credentials

Naming setup and teardown

Try to use name that describes what is done.
Possibly use an existing keyword.
More abstract names acceptable if a setup/teardown contain unrelated steps.
Listing steps in name is duplication and a maintenance problem (e.g. Login to system, add user, activate alarms and check balance).
May need to use something generic like Initialize system.
BuiltIn keyword Run Keywords can work well if you have keywords for all lower level steps ready. For example | Run Keywords | Login to system | Add user | Activate alarms |.
Not reusable so best used when certain setup/teardown scenario is needed only once.
Everyone working with these tests should always understand what a setup/teardown does.
*** Settings ***

Test Setup   Login To System, Add User, Activate Alarms And Check Balance

*** Settings ***

Test Setup   Initialize System

Documentation

Test suite documentation

Often a good idea to add documentation to lowest level suites containing tests.
Higher level suites do not need documentation that often.
Should contain background information, why tests are created, notes about execution environment, etc.
Do not just repeat test suite name.
Better not to have documentation all if it is not really needed.
Do not include too much details about test cases.
Tests should be clear enough to understand alone.
Duplicate information is waste and maintenance problem.
Documentation can contain links to more information.
Consider using test suite metadata if you need to document information represented as name-value pairs (e.g. Version: 1.0 or OS: Linux.)
account_withdrawal.txtaccount_withdrawal.txt
*** Settings ***

Documentation    Tests Account Withdrawal.

*** Settings ***

Documentation    Tests to verify that account withdrawals succeed and

...              fail correctly depending from users account balance

...              and account type dependent rules.

...              See http://internal.example.com/docs/abs.pdf

Metadata         Version    0.1

Test case documentation

Test normally do not need documentation.
Suite's name and documentation and test's name should give enough background information.
Test cases' structure should be clear enough without documentation or other comments.
Tags are generally more flexible and provide more functionality (statistics, include/exclude, etc.) than documentation.
Sometimes test documentation is useful. No need to be afraid to use it.
valid_login.txtvalid_login.txt
*** Test Cases ***

Valid Login

  [Documentation]    Opens a browser to login url, inputs username

  ...                and password and checks the welcome page is open.

  ...                This is a smoke test. Created in iteration 3.

  Open Browser   ${URL}  ${BROWSER}

  Input Text     field1  ${UN11}

  Input Text     field2  ${PW11}

  Click Button   button_12

  Title Should Be  Welcome Page

  [Teardown]     Close Browser

*** Test Cases ***

Valid Login

  [Tags]  Iteration-3  Smoke

  Open Login Page

  Input Username    ${VALID USERNAME}

  Input Password    ${VALID PASSWORD}

  Submit Credentials

  Welcome Page Should Be Open

  [Teardown]   Close Browser

User keyword documentation

Not needed if keyword is relatively simple.
Good name and clear structure should be enough.
Important usage is documenting arguments and return values.
Shown in RIDE (for example in keyword completion) and in resource file documentation generated with libdoc.py.
Test suite structure

Tests in a suite should be related to each others.
Same setup/teardown is a good indicator.
Should not have too many tests (max 10) in one suite unless they are data-driven.
Tests should be independent.
Initialization using setup/teardown
Sometimes dependencies between tests cannot be avoided.
It can be, for example, too time taking to initialize all tests separately.
Never have long chains of dependent tests (max 4-5).
Consider verifying the status of the previous test using ${PREV TEST STATUS} variable.
Test case structure

Test case should be easy to understand.
One test case should be testing one thing.
Things can be small (part of single feature) or large (end-to-end).
Select suitable abstraction level.
Use abstraction level consistently (single level of abstraction principle).
Only include information that is relevant for the test case.
Two kinds of test cases
Workflow tests
Data-driven tests
Workflow tests

Generally has these phases:
Preconditions (optional, often in setup)
Action (do something to the system)
Verification (must have one!)
Cleanup (optional, always in teardown to make sure it is executed)
Keywords describe what a test does.
Use clear keyword names and suitable abstraction level.
Should contain enough information to run manually.
Should never need documentation or commenting to explain what the test does.
Different tests can have different abstraction levels.
Tests for a detailed functionality are more precise.
End-to-end tests can be on very high level.
One test should use only one abstraction level
Different styles
More technical tests for lower level details and integration tests
"Executable specifications" act as requirements
Use domain language
Everyone (including customer/product owner) should always understand
No complex logic
No for loops or if/else constructs
Use variable assignments with care
Test cases should not look like scripts
Max 10 steps, preferably less
Data-driven tests

One high-level keyword per test
Different arguments create different tests
The keyword often contains similar workflow as workflow tests and is created in the same test case file
Recommended to use Test Template functionality.
No need to repeat the keyword multiple times.
Easier to test multiple variations in one test.
Possible, and recommended, to name column headings
If a really big number of tests is needed, consider generating them based on an external model.
``` * Settings * Test Template Invalid Login

* Test Cases * USERNAME PASSWORD Invalid Username invalid ${VALID PASSWORD} Invalid Password ${VALID USERNAME} invalid Invalid Both invalid invalid Empty Username ${EMPTY} ${VALID PASSWORD} Empty Password ${VALID USERNAME} ${EMPTY} Empty Both ${EMPTY} ${EMPTY}

* Keywords * Invalid Login [Arguments] ${username} ${password} Input Username ${username} Input Password ${password} Submit Credentials Error Page Should Be Open ```

User keywords

Should be easy to understand
Same rules as with workflow tests
Different abstraction levels
Can contain some programming logic (for loops, if/else)
Especially on lower level keywords
Complex logic in libraries rather than in user keywords
Variables

Encapsulate long and/or complicated values
Pass information from command line
Pass information between keywords
Variable naming

Clear but not too long names
Can use comments in variable table to document them more
Use case consistently
Lower case with local variables only available inside certain test or keyword
Upper case with others (global, suite or test level)
Both space and underscore can be used as word separator
Recommended to list also variables that are set dynamically in the variable table
Set typically using Set Global/Suite/Test Variable keywords
The initial value should explain where/how the real value is set
``` * Variables *

URL for CI server. Override with your local server if run elsewhere.

${SERVER URL} http://sre-12.example.com/ ${BROWSER} Actual value set dynamically at suite setup

* Keywords * Title Should Begin With [Arguments] ${beginning} ${title} = Get Title Should Start With ${title} ${beginning}

Suite Setup ${BROWSER} = Get Current Browser Set Suite Variable ${BROWSER} ```

Passing and returning values

Common approach is to return values from keywords, assign them to variables and then pass them as arguments to other keywords
Clear and easy to follow approach
Looks like programming
Alternative approach is using Set Test Variable keyword
No need to have any programming style in test case level
More complex to follow, reusing keywords harder
Avoid below test case level
valid_login.txtvalid_login.txt
*** Test Cases ***

Withdraw From Account

  ${status} =   Withdraw From Account    $50

  Withdraw Should Have Succeeded     ${status}



*** Keywords ***

Withdraw From Account

  [arguments]   ${amount}

  ${status} =   Withdraw From User Account  ${USER}  ${amount}

  [return]      ${status}



Withdraw Should Have Succeeded

  [arguments]   ${status}

  Should Be Equal   ${status}   SUCCESS

*** Test Cases ***

Withdraw From Account

  Withdraw From Account    $50

  Withdraw Should Have Succeeded



*** Keywords ***

Withdraw From Account

  [arguments]   ${amount}

  ${status} =   Withdraw From User Account  ${USER}  ${amount}

  Set Test Variable   ${STATUS}    ${status}



Withdraw Should Have Succeeded

  Should Be Equal   ${STATUS}   SUCCESS

Avoid sleeping

Sleeping is very fragile
Safety margins cause too long sleeps on average
Instead of sleep, use keyword that polls has certain action occurred
Should have a maximum time to wait
Keyword names often starts with Wait Until ...
Possible to wrap other keywords inside BuiltIn keyword Wait Until Keyword Succeeds
Sometimes sleeping is easiest solution
Always use with care and never in user keywords that are used often by tests or other keywords
Can be useful in debugging to stop execution
Although DialogsLibrary often works better for that purpose
