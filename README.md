# Guide

This repo has been separted into one environment per directory. Each contains it's own HCP Terraform workspace, linked
to the cloud.

# AWS Accounts

# Mgt
There is a central management account in AWS, used to control other accounts. The MGT account is in eu-west-2. It's
controlled by the root user login.

# Dev
I have set up a member account in the management account's organisation. 
By default an admin role is created 'OrganizationAccountAccessRole`. By default this allows the root principal in the
management account to assume Admin privileges in the member account.

# Terraform access to dev
I created a policy that allowed Terraform to assume the `OrganizationAccountAccessRole` in the dev
member account. I used [this guide](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_accounts_access-cross-account-role.html)
using `AssumeRole` for the dev account's  'OrganizationAccountAccessRole` role in the policy. I then used the above
guide to create a specific role in the Management account that had that permission policy and a trust policy for
the Terraform identity provider using [this guide](https://aws.amazon.com/blogs/apn/simplify-and-secure-terraform-workflows-on-aws-with-dynamic-provider-credentials/)
to create a trusted OIDC connection between HCP Terraform and the AWS management account.

I additionaly updated the `OrganizationAccountAccessRole` in the dev account to allow the Terraform role. But this may
not be necessary, as I think the default `root` AWS principal covers all principals (including Terraform).

## Terraform CLI access
Authentication with HCP Terraform (Cloud) from the CLI has been configured using an API token, generated in their UI and saved in
`.terraformrc` in my home directory. This allows plan and apply runs to be triggered from the terminal.





# TODO
Re setup dev to connect to a new account when the account quota limits have been increased (currently only one).

Consider creating a variable set in HCP Terraform that allows the OIDC connection to apply across all
workspaces/environments. This will need a new trust policy for the Terraform role that allows it to assume admin in the
sub-accounts (management is the parent) once created.