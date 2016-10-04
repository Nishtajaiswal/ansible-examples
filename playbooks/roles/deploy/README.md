deploy
-------------------------------
Deploy the Application Stack into AWS.

## Role Actions.
1. Create the IAM Role that will be attached to the ASG.
2. Attach the IAM policies that the role above requires.
3. Create the EC2 instance.

## Variables this role is dependent on.

**The variables in `main.yml` are a role dependency.**
```yaml
zones: "{{ aws_region | zones() }}"
aws_vpc_id: "{{ aws_vpc_name | get_vpc_id_by_name(aws_region) }}"
aws_mgmt_vpc_id: "{{ 'Management' | get_vpc_id_by_name(aws_region) }}"

aws_private_subnets: "{{ aws_vpc_id | get_subnet_ids(aws_private_cidrs.values(), aws_region) }}"
aws_public_subnets: "{{ aws_vpc_id | get_subnet_ids(aws_public_cidrs.values(), aws_region) }}"
aws_persistent_subnets: "{{ aws_vpc_id | get_subnet_ids(aws_persistent_cidrs.values(), aws_region) }}"


mgmt_global_sg_id: "{{ 'Management-Public' |get_sg(aws_mgmt_vpc_id, aws_region) }}"
mgmt_public_cidrs: "{{ 'Management-Public' |get_sg_cidrs(aws_mgmt_vpc_id, aws_region) }}"
aws_public_elb_sg_id: "{{ aws_public_elb_sg | get_sg(aws_vpc_id, aws_region)  }}"

office_env_sg_name: "{{ office_sg_name }}-{{ aws_vpc_name }}"
office_sg_id: "{{ office_env_sg_name | get_sg(aws_vpc_id, aws_region) }}"

mgmt_sg_ids:
  - "{{ mgmt_global_sg_id }}"

aws_public_elb_sg_ids:
  - "{{ aws_public_elb_sg_id }}"

office_sg_ids:
  - "{{ office_sg_id }}"

mgmt_ssh_rules: "{{ mgmt_public_cidrs | make_sg_rules(ports=ssh_ports) }}"
```
