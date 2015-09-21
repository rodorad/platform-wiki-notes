
Priority

Defines importance when the defect should be fixed. If defect A has higher priority than defect B defects A should be fixed before defect B.

Possible values:

    Highest - The defect must be fixed immediately. It causes that system cannot be used by customer or cannot be tested. It happens on every or almost every installation. Such defect is treated as show stopper and may change normal task schedule.
    High - The defect must be resolved as soon as possible. Some function of system doesn't work and customer may wait some time for next scheduled build or minor version. It should not change task schedule significantly.
    Medium - Some function of system doesn't work and customer may wait some time for next major version and when other more important defects are fixed
    Low - This defect doesn't have direct impact on function of system and may be fixed it when it is enough time.
    Lowest - There is no need to fix but we want to file it. It doesn't have to be fixed.
    Undecided - Initial stage when priority is not set yet. 

 
Severity

It defines how the defect impacts the system and its functionality. Setting this field should be generally independent on particular customer or temporary needs. It doesn't specify when issue should be fixed.

Possible values:

    Critical - The whole system or key components crashes continuously. Defect causes data corruption or data loss. Major function is unusable and there is no workaround to achieve required results. It should be set for significant performance degradation defects.
    High - The whole system or key components crashes occasionally. Defect does not cause data corruption or data loss. Major function is unusable but there is workaround to achieve required results. It should be set for minor performance degradation defects.
    Medium - No system or key component crashes. Defect does not cause data corruption or data loss. Given function gives incorrect results but its area is limited. Major documentation defects that have impact of system understanding.
    Low - Cosmetic issues that doesn't have impact on results produced by system.  If should be set for look and feel defects and for minor documentation defects
    None - Not enough information to qualify how defect impacts the system



Some examples:

    Critical and Highest  - System cannot be used by user - DP2 console crashes and nobody can log into the system.
    Low and Highest - Typo mistake on login page. It looks very unprofessional. System works but defects affects customer perception
    Critical and Lowest - System crashes every time when user click on link that is not used normally.
    Low and Lowest - Labels on page are not aligned ideally.

