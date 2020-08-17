# Playbooks for interacting with the phpipam api
With a working phpipam install you can enable the api and use these interactions inside of playbooks.
You will need to configure the app id and create a user that will be accessing this api in order to
gather a token.

### Setting up the API for usage in Ansible
Inside of the gui you will need to navigate to the API section under administration. 
Here you can configure the app id for the API
Next you will need to navigate to the Users section to create your API user

### Interacting with the API
To find the subnets and to populate specific variables you'll need to poke around the API with postman or curl. 

using curl to return a token:
`curl -k https://ipam.example.com/api/phpipamappid/user/ -X POST -u <username>:<password> `

grabbing the token from the returned json output:
`curl -k https://ipam.example.com/api/phpipamappid/sections/ --header "token: .J1e9ipFZkPE6EvIRAqEf9hp" -X GET `

Once you have the ID of the sections this can be used to get the subnets inside this section. 
`curl -k https://ipam.example.com/api/phpipamappid/sections/{ID}/subnets --header "token: .J1e9ipFZkPE6EvIRAqEf9hp" -X GET `

This will list all of the subents in this section, and you'll want to grab the id from the returned objects
``` "data": {
"id": <number>,
...
```
This is where the `subnet_id:` variable is populated. 

### Reserve IP example Playbook
main.yml playbook is an example of using the URI module
to make an api call, register the output to populate variables,
making calls to register an ip, and update phpipam's database. 

To generate the encrypted value to populate the vault password for the
api_pass variable you will need to generate this with ansible-vault. 
https://docs.ansible.com/ansible/2.4/vault.html

One all of the values in the group_vars/all file are set to match your environment
and only then will the playbook succeed. 
Making the call to the API will pull down the token
which is then stored in a variable and used throughout the playbook

phpipam documentation
https://phpipam.net/api/api_documentation/

* [GPL license](https://github.com/brgalloway/phpipam-api/blob/master/LICENSE)
