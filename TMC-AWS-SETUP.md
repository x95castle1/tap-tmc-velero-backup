# Setting up AWS Credential for Data Project If Target Location Already Exists

This 

## 

generate template
document assume role policy document (need this to update the trust relationship of the existing role VMwareTMCProviderCredentialMgr)
document DataProtectionPolicy statements related to the new bucket
delete DataProtectionPolicy, and DataProtectionRole from template (you should only have bucket)
apply modified cloudformation template (should create only a bucket)
edit VMwareTMCProviderCredentialMgr, to add in the template’s generated assumeRolePolicy Statement (principle + condition, etc.)
edit the dataprotection.tmc.cloud.vmware.com policy to add in additional permissions for the new bucket (the DataProtectionPolicy statements prior removed from template)
create target location in TMC with success!

https://us-east-1.console.aws.amazon.com/iamv2/home?region=us-east-1#/roles/details/VMwareTMCProviderCredentialMgr?section=trust_relationships 


http://dataprotection.tmc.cloud.vmware.com/