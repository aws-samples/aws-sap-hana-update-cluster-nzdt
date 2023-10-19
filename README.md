# SAP HANA Upgrade Automation using Ansible
# Version: 1.5 
# Release: 06/10/2023


# Although this repository is released under the Open Source - MIT-0 license, its Ansible Playbook 
# use the third party project. The project's licensing includes the Ansible license.

This playbook executes below tasks :

1. Upgrade SAP HANA Server Components(SPS) on clustered environment


### Usage 
ansible-playbook -i <inventoryfile> --ask-vault-pass -e "SID=<SID>" -e "MEDIASRC=<s3/fs>" -e "MEDIALOC=<locationofSARfile"  ./patch_sap_hana.yml

### Usage Examples
For example, in case the inventory file is “myinventory”, the HANA DB SID is “HDB”, the MEDIA source is from S3 bucket and the SAR file is in “s3://hanapatch/” then use the following command:

ansible-playbook -i myinventory --ask-vault-pass -e "SID=HDB" -e "MEDIASRC=s3" -e "MEDIALOC=s3://hanapatch/"  ./patch_sap_hana.yml


An other example, in case the inventory file is “myinventory”, the HANA DB SID is “HDB”, the MEDIA source is from file system and the SAR file is in "/tmp/hanapatch/", then use the following command:

ansible-playbook -i myinventory --ask-vault-pass -e "SID=HDB" -e "MEDIASRC=fs" -e "MEDIALOC=/tmp/hanapatch/"  ./patch_sap_hana.yml


### Prerequisites

1. root passwords on all HANA Nodes are the same

2. <sid>adm password on all HANA Nodes are the same

3. to know the SYSTEM user password for HANA SYSTEMDB and tenant database

4. HANA SAR file (patch file) required is placed in an S3 Bucket or on File system on both servers at same path (no other files expect the HANA patch file)

5. In case of media sourced from S3, ensure EC2 instances have access to S3 bucket (e.g. using IAM roles)

6. All required additional OS packages are installed prior patching

7. by default the playbook expects the HANA nodes are part of the following group in the inventory file
   SAP_{{SID}}_hana_ha      -   group for HANA HSR cluster nodes

8. The playbook expects only one SAR file (DB patch file) in the source S3 bucket or file system location

9. create a file called passvault.yml that contains the encrypted passwords



### Input variables overview 

Variable Name     Description                                                          Type           Multi Choice     
SID               HANA SID to be patched                                               Text (3)       N/A
SYSTEMDBPWD       SYSTEM user password for the HANA SYSTEMDB                           Text           N/A
SYSTEMTNTPWD      SYSTEM user password for the HANA Tenant DB                          Text           N/A
ROOTPWD           root Linux user password on the nodes (must be set the same)         Text           N/A
SIDADMPWD         <sid>adm Linux user password on the nodes (must be set the same)     Text           N/A
MEDIASRC          Media Source for the patch file                                      Text           Yes - "s3" / "fs"
MEDIALOC          Media Location on the source media                                   Text           N/A

The playbook excepts the ROOTPWD, SIDADMPWD, SYSTEMDBPWD and SYSTEMTNTPWD varaibles encrypted in a file called "passvault.yml".

The other variables can be passed in as argument in command line.

To create a passvault.yml file that contains the encrypted passwords, run:

ansible-vault encrypt_string 'theactualrootpassword' --name 'ROOTPWD' | tee -a passvault.yml
ansible-vault encrypt_string 'theactualsidadmpassword' --name 'SIDADMPWD’ | tee -a passvault.yml
ansible-vault encrypt_string 'theactualsystemtenantpassword' --name 'SYSTEMTNTPWD' | tee -a passvault.yml
ansible-vault encrypt_string 'theactualsystemdbpassword' --name 'SYSTEMDBPWD' | tee -a passvault.yml

Make sure tee commands places all variables in a new line in the file.



# Examples
# ########

### HANA Upgrade - patch_sap_hana.yml (HANA Server upgrade Media Source: s3)

Playbook needs the following inputs:

- `ENTER HANA SYSTEM ID`: D1T
- `HANA SYSTEMDB SYSTEM Password`: <HANA System DB Password>   (in passvault.yml)
- `HANA TENANT SYSTEM Password`: <HANA Tenant DB Password>     (in passvault.yml)
- `ROOT PASSWORD`: <Host Root Password>                        (in passvault.yml)
- `SIDADM Password`: <Host SIDADM Password>                    (in passvault.yml)
- `HANA Server upgrade Media Source`: s3
- `HANA Server Upgrade Media Location`: s3://hanaupgradeautomation/GBT/

### HANA Upgrade - patch_sap_hana.yml (HANA Server upgrade Media Source: fs)

Playbook needs the following inputs:

- `ENTER HANA SYSTEM ID`: S1T
- `HANA SYSTEMDB SYSTEM Password`: <HANA System DB Password>
- `HANA TENANT SYSTEM Password`: <HANA Tenant DB Password>
- `ROOT PASSWORD`: <Host Root Password>
- `SIDADM Password`: <Host SIDADM Password>
- `HANA Server upgrade Media Source`: fs
- `HANA Server Upgrade Media Location`: /hana/shared/hanamedia

## Authors

Gergely Cserdi(AwS), Akshay Parkhi(AWS) - 6/10/2023 - 1.5 version release.


## Copyright
Copyright 2023 Amazon.com, Inc. or its affiliates. All Rights Reserved.
    

## License

This library is licensed under the MIT-0 License. See the LICENSE file.

