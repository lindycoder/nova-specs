..
 This work is licensed under a Creative Commons Attribution 3.0 Unported
 License.

 http://creativecommons.org/licenses/by/3.0/legalcode

===============================================================
nova-api can check hypervisor availability based on boot params
===============================================================

https://blueprints.launchpad.net/nova/+spec/hypervisor-availability

Add an endpoint to determine which hypervisors fit a given set of parameters
similar to the nova create.

Problem description
===================

From an administrator perspective, it may be hard to assert how many hosts are
available for a given set of parameters like those used in nova create.  So by
invoking this endpoint and giving it the same a set of parameters, you receive
a list of weighed hosts for which that configuration fits.

Use Cases
---------

For a given flavor, a deployer can know how many hosts are available to receive
a new instance of that said flavor.

Proposed change
===============

I propose adding a new api endpoint in these forms:

* REST API:

    GET: /v2.1/​{tenant_id}​/os-hypervisors/schedule-test

    Result:
    {
        "schedule-test": {
            "hypervisor" : [
                {
                    "id": 1,
                    "hypervisor_hostname": "shizzle",
                    "weight": 100
                }
            ]
        }
    }

* CLI Client

    nova hypervisor-schedule-test

    Result:
    +----+---------------------+--------+
    | ID | Hypervisor hostname | Weight |
    +----+---------------------+--------+
    | 1  | shizzle             | 100    |
    +----+---------------------+--------+

* Python Client

    client.hypervisors.schedule_test()

    Result:
    [ WeighedHypervisor(id=1, hostname="shizzle", weight=100) ]

Parameters should be the exact same parameters as nova create

This operation will NOT reserve nor create any state change in any object.

This will all be possible by reproducing/reusing the logic of the method
nova.scheduler.filter_scheduler.FilterScheduler::_schedule by calling
get_filtered_hosts and get_weighed_hosts.

On the Cells topic, this should pass in the cells scheduling as if an instance
was actually created.

Posting to get preliminary feedback on the scope of this spec.
