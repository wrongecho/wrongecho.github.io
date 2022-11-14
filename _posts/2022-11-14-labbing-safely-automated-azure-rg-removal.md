## Labbing Safely on Azure - Automating Resource Group Removal

Labbing in the Cloud can be dangerous! We've all heard stories about accidently leaving a resource running overnight and then waking up to a 5k bill.

Whilst Azure budget alerts are a great indicator that you've left something running, I'd really prefer to have everything clean itself up automatically after me.

Luckily enough, we can use Azure [Automation Accounts](https://learn.microsoft.com/en-us/azure/automation/automation-create-standalone-account?tabs=azureportal) to automatically run a PowerShell one-liner to clean-up all resource groups every night!

> Note: The first 500 minutes of automation every month are free!

1. Create a new resource group for your automation (e.g. `automation-rg`)
1. Tag existing resource groups you'd like to keep (including `automation-rg`) with `status`:`protected`
1. Create a new automation account in `automation-rg`
1. Assign the service principal Owner/Contributor permissions for your subscription
![iam.png](/img/2022-11-14-labbing-safely-azure/iam.png)
1. Create a PowerShell runbook with the following commands:

    ```
    Connect-AzAccount -Identity

    Get-AzResourceGroup | Where-Object {$_.Tags.status -ne "protected"} #| Remove-AzResourceGroup -Force
    ```

1. Test the runbook and ensure the output shows all the "unprotected" resource groups you'd like to remove
1. Once you're happy, remove the comment from the second line and test the script removes the resource groups as expected
1. Publish your runbook and add/link a schedule to run nightly (e.g. 2 AM)

![automation-rg.png](/img/2022-11-14-labbing-safely-azure/automation-rg.png)