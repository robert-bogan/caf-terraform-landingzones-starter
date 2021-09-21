
### Generate asvm for {{ asvm_folder }}

```bash
rover login -t {{ config.platform_identity.tenant_name }}.onmicrosoft.com

rover \
{% if config.platform_identity.azuread_identity_mode != "logged_in_user" %}
  --impersonate-sp-from-keyvault-url {{ keyvaults.cred_subscription_creation_landingzones.vault_uri }} \
{% endif %}
  -lz /tf/caf/landingzones/caf_solution \
  -var-folder {{ config.configuration_folders[base_folder_asvm].destination_base_path }}{{ config.configuration_folders[base_folder_asvm].destination_relative_path }}/{{ level }}/{{ base_folder_asvm }}/{{ asvm_folder }}/subscription \
  -tfstate_subscription_id {{ config.platform_core_setup.enterprise_scale.primary_subscription_details.subscription_id }} \
  -tfstate {{ tfstates_asvm[asvm_folder].subscription.tfstate }} \
  --workspace {{ tfstates_asvm[asvm_folder].workspace }} \
  -log-severity {{ config.gitops.rover_log_error }} \
  -env {{ config.caf_terraform.launchpad.caf_environment }} \
  -level {{ level }} \
  -a plan

rover logout

```
Once you have executed the rover apply to create the subscription, you need to execute the rover ignite to generate the instructions for the next steps.

Note you need to logout and login as a caf_maintainer group member

```bash
rover login -t {{ config.platform_identity.tenant_name }}.onmicrosoft.com

rover ignite \
  --playbook /tf/caf/starter/templates/platform/e2e.yaml \
  -e base_templates_folder=/tf/caf/starter/templates/platform \
  -e config_folder=/tf/caf/orgs/contoso \
  -e scenario=contoso \
  -e model=platform \
  -e boostrap_launchpad=false \
  -e deploy_subscriptions=false

```

# Next steps

When you have successfully created the subscription and re-executed the rover ignite you can move to the next step.

 [Deploy the platform resources in the subscription](../resources/readme.md)