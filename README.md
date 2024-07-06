# Changes to laser_assembler

## Add suffix to service name

> [!NOTE]
> If the suffix parameter is not found, the node works as before.

* launch

```
<node type="laser_scan_assembler" pkg="laser_assembler" name="my_assembler1">
    <remap from="scan" to="/rexrov2/sonar_1"/>
    <param name="max_scans" type="int" value="50" />
    <param name="fixed_frame" type="string" value="/rexrov2/base_link" />
    <param name="suffix" type="string" value="1" />
</node>

<node type="laser_scan_assembler" pkg="laser_assembler" name="my_assembler2">
    <remap from="scan" to="/rexrov2/sonar_2"/>
    <param name="max_scans" type="int" value="50" />
    <param name="fixed_frame" type="string" value="/rexrov2/base_link" />
    <param name="suffix" type="string" value="2" />
</node>
```

* C++

```
#include <ros/ros.h>
#include <laser_assembler/AssembleScans.h>
using namespace laser_assembler;
int main(int argc, char **argv)
{
  ros::init(argc, argv, "test_client");
  ros::NodeHandle n;
  ros::service::waitForService("assemble_scans_1");
  ros::service::waitForService("assemble_scans_2");
  ros::ServiceClient client1 = n.serviceClient<AssembleScans>("assemble_scans_1");
  ros::ServiceClient client2 = n.serviceClient<AssembleScans>("assemble_scans_2");
  AssembleScans srv;
  srv.request.begin = ros::Time(0,0);
  srv.request.end   = ros::Time::now();
  if (client1.call(srv))
    printf("1: Got cloud with %u points\n", srv.response.cloud.points.size());
  else
    printf("1: Service call failed\n");

  if (client2.call(srv))
    printf("2: Got cloud with %u points\n", srv.response.cloud.points.size());
  else
    printf("2: Service call failed\n");
  return 0;
}

```