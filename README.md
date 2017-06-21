# Ansible Code
This is the Ansible modules and playbooks that is used for demo of KeyVault


# Keys
SSH Keys used by Ansible

# Ansible run Example

ansible-playbook -i $inventory $playbook  -u $user --private-key=keys/key.pem -extra-vars "platform=$provider" --limit $service-$env-build

Inventory can be a static file but we use the Azure dynamic inventory script.
User is the account that was used by Infra provisioning on the VM instances
Key is the SSH key that was injected into the VM instances for $user
$service-$env-build is a placeholder to limit the ansible run on a particular resource group.

