Role Name
=========

Setup AWS CloudWatch Alarms.

**LIMITATION**:

* Setup EC2 instances as a resources and lookup the tag `ansible_host` in all instances of the default region.
* Setup ELB Target Group Alarms

Requirements
------------

Ansible >= 2.3

Role Variables
--------------

`aws_alarms`: is the list of Alarm definition. See more
at [documentation page](http://docs.ansible.com/ansible/latest/ec2_metric_alarm_module.html).

Dependencies
------------

TODO

Example Playbook
----------------

    - hosts: servers
      vars:
        aws_alarms:
          - name: instance-name-CPU_Utilization
            name_sufix: "CPU_Utilization"
            filter_type: "ec2_tag"
            filter_host:
              -  "lookup-in-tag-ansible_host"
            region: us-east-1
            metric: "CPUUtilization"
            namespace: "AWS/EC2"
            statistic: Average
            comparison: ">="
            threshold: 75.0
            period: 60
            evaluation_periods: 3
            unit: "Percent"
            alarm_actions: ["arn:aws:sns:us-east-1:<AWS_ACCOUNT_ID>:<SNS_ALERT>"]
            insufficient_data_actions: ["arn:aws:sns:us-east-1:<AWS_ACCOUNT_ID>:<SNS_ALERT>"]
            ok_actions: ["arn:aws:sns:us-east-1:<AWS_ACCOUNT_ID>:<SNS_ALERT>"]

          - name: tg-alarm-prefix-Unhealthy-Hosts
            namespace: "AWS/ApplicationELB"
            resources:
              - name_sufix: "sufix-alarm"
                dimensions:
                  LoadBalancer: my-elbv2-name/ffffffffffffffff
                  TargetGroup: targetgroup/my-tg-name/ffffffffffffffff
            region: us-east-1
            metric: "UnHealthyHostCount"
            statistic: Average
            comparison: ">="
            threshold: 1
            period: 60
            evaluation_periods: 10
            unit: "Count"
            alarm_actions: ["arn:aws:sns:us-east-1:<AWS_ACCOUNT_ID>:<SNS_ALERT>"]
            insufficient_data_actions: ["arn:aws:sns:us-east-1:<AWS_ACCOUNT_ID>:<SNS_ALERT>"]
            ok_actions: ["arn:aws:sns:us-east-1:<AWS_ACCOUNT_ID>:<SNS_ALERT>"]

      roles:
         - { role: aws-alarms.chaordic }

License
-------

GPLv3

Author Information
------------------

Cloud Operations Team, Linx+Neemu+Chaordic
