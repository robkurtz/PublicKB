{{{
  "title": "Creating and Applying Autoscale Policies",
  "date": "10-21-2016",
  "author": "Chris Little",
  "attachments": [],
  "contentIsHTML": false
}}}

### Description
The CenturyLink Cloud platform now supports both Vertical Autoscale of CPU capacity for servers as well as Horizontal Autoscale of servers. This makes it possible to scale servers up and down (vertical) or out and in (horizontal) based on utilization, ensuring optimal deployment of resources for cloud environments under a variety of conditions.

How does Autoscale work? For vertical Autoscale, servers that exceed a user-defined CPU utilization threshold instantly scale up, and servers that go below a user-defined CPU utilization threshold scale down (and reboot) during a user-defined window. In the case of horizontal Autoscale, groups of servers that exceed a user-defined CPU/RAM utilization threshold scale out by powering on one or more additional servers in the group, and groups of servers that go below a user-defined CPU/RAM utilization threshold scale in by powering off one or more servers in the group.

For example, consider a vertical Autoscale policy that sets a range of 4 to 6 CPUs for a server. The server is set to scale up by 2 CPUs if the server is running at 90% utilization for longer than 15 minutes. If the server is at 15% CPU utilization for longer than 15 minutes, then the policy scales the server down IF this event occurs during the user-defined scale down window. (A similar story would apply for horizontal Autoscale using number of servers instead of number of CPUs.)

![Autoscale CPU](../images/createautoscale-autoscale-cpu02.png)

This KB article describes all of the steps for creating, applying, and testing both vertical and horizontal Autoscale policies.

### Audience
* CenturyLink Cloud customers (operations staff)

### Prerequisites
* CenturyLink Cloud account
* A running server on which to apply an Autoscale policy

### Configuring Vertical Autoscale - New Policy
1. Log into the [Control Portal](https://control.ctl.io/).

2. From the Navigation Menu, click **Infrastructure > Policies**. This takes you to the page where you can create and manage new policies that can later be applied to servers.

3. On the left side of the screen, click **Vertical Autoscale**. This page shows all existing vertical Autoscale policies, the details of each, and the servers assigned to them.

4. Click **vertical autoscale policy**. Define the general policy characteristics.

  ![Autoscale Policy](../images/createautoscale-autoscale-parameters.png)

  * name: name of the autoscale policy
  * cpu range: the allowable minimum and maximum number of CPUs for the server. This range is used for each server that the policy is applied to. If you have three servers each using the same policy, they each have the capability to use the maximum number of CPUs.
  * threshold period: how long a server must be at the minimum or maximum usage before an Autoscale action occurs.
  * cool down period: how long to wait after performing one Autoscale event before considering another. This setting helps prevent a rapid fire set of Autoscale events before the server has a chance to recognize the positive effects from the initial scale event. If a server is likely to have temporary spikes that don't warrant a scaling event, choose a longer period.
  * scale up: the first value defines the upper limit of CPU utilization. When a server is at the upper limit for a single threshold period, a scale up event occurs (unless it occurs within a cool down period).
  * scale up increment: choose how many CPUs to add (1, 2, 4) during a particular scaling event.
  * scale down: the first value determines the lower limit of CPU utilization. When a server is at the lower limit for a single threshold period within the scale down window, a scale down event occurs.
  * scale down window: UTC time that you can tolerate a reboot of the system to reduce the CPU allocation (for example when most users may be offline). Choose a small window if a reboot is particularly disruptive to the system. However, make sure that the window is wide enough to catch periods of low utilization.

5. Review the notice at the bottom of the page. The notice indicates that if you apply a policy to a server that is outside the bounds of the policy's CPU range (e.g., a policy with a maximum CPU count of 6 is applied to a server that is currently using 10 CPUs), then no scale up events occurs until the first scale down event pulls the server within the policy's range.

6. Click **Create**.

7. Your policy is now part of the list of vertical Autoscale policies.
   ![Autoscale Policy](../images/createautoscale-autoscale-cpu08.png)

### Configuring Vertical Autoscale - Existing Server
Vertical Autoscale policies can be added to existing servers or during the server creation process. These steps show how to apply to an existing server. (Similar steps below are followed after setting the **autoscale** option to **on** when creating a server.

1. Locate a Windows Server 2012/2012 R2 Datacenter Edition, Red Hat Enterprise Linux 5/6/7, or Ubuntu 12/14 x64 server to apply a vertical Autoscale policy to.
   * If you attempt to add a vertical Autoscale policy to a server that isn't based on one of the previously mentioned OSes, there is no option available to do so.

2. From the server settings page, mouseover the CPU utilization chart and click **EDIT** when the button appears. This displays the Edit CPU panel.

   ![Autoscale Policy](../images/createautoscale-autoscale-server-settings.png)

3. Flip the Autoscale slider to **ON** and choose an Autoscale policy from the drop-down.
   * If you flip this switch from the Create Server page, you see a preview of all the details of the policy after you select it. From the server settings page, only the policy name is displayed.
   * Please read any notices appearing next to the **apply** button.
   * If your server has not already been set up to add resources without a reboot, then the first time you assign an Autoscale policy, the server is rebooted.

4. Click **apply** to save the updated server settings. Your server is now listed next to the associated policy on the Autoscale Policies page.

5. Next, create a significant amount of load on the server in order to exceed the upper threshold for a sustained period of time. In the example below, see that the CPU utilization is consistently above 88%.
   ![Autoscale Policy](../images/createautoscale-autoscale-cpu13.png)

6. After the threshold period has passed, note that the server has a new CPU (without rebooting) and an entry was added to the activity history. The activity history entry shows the average CPU amount that triggered the Autoscale, how long the threshold period was, and how many CPUs the server has now.
   ![Autoscale Policy](../images/createautoscale-scale-up-event.png)

7. To test scale down, visit your Autoscale policy and broaden the **scale down window** timeframe. This ensures that the period of low usage coincides with the window where scale down is allowed.

8. Wait until the cool down period has passed, and watch for a scale down (and reboot) to occur.
   * Note that a scale down event takes the server back to the minimum number of CPUs defined in the Autoscale policy.
   * The activity history entry shows the average CPU amount that triggered the Autoscale, how long the threshold period was, and how many CPUs the server has now. In this scenario, see that the server has a single CPU allocated after the scale down occurs.

   ![Autoscale Policy](../images/createautoscale-scale-down-event.png)

9. Autoscale policies can be changed at any time and those changes are instantly reflected on the related servers. In addition, at any point you can remove the policy from a server, and go back to manually changing CPU capacity.

### Configuring Horizontal Autoscale - New Policy
1. Log into the [Control Portal](https://control.ctl.io/).

2. From the Navigation Menu, click **Infrastructure > Policies**. This takes you to the page where you can create and manage new policies that can later be applied to servers.

3. On the left side of the screen, click **Horizontal Autoscale**. This page shows all existing vertical Autoscale policies, the details of each, and the servers assigned to them.

4. Click **horizontal autoscale policy**. Define the general policy characteristics.

  ![Autoscale Policy](../images/createautoscale-h-autoscale-parameters.png)

  * name: name of the autoscale policy
  * minimum servers: defines the minimum number of for the servers to remain on at all times.
  * maximum servers: the max server count is determined by the number of provisioned servers within the group the policy is applied to.
  * metrics: choose which metrics to include in the threshold, CPU and/or memory. This determines what metrics the threshold can be set on when defining the scale out and scale in triggers.
  * increment: choose how many servers to turn on (1, 2, 4) during a particular scaling event.
  * cpu greater than: define the upper limit of utilization. There are percentage fields for each of the metrics you set. If you selected both, you have the operator option to select whether both thresholds must be reached or just one of them. When a server is at the upper limit as defined for a single threshold period, a scale out event occurs (unless it occurs within a cool down period).
  * memory greater than: define the upper limit of utilization. There are percentage fields for each of the metrics you set. If you selected both, you have the operator option to select whether both thresholds must be reached or just one of them. When a server is at the upper limit as defined for a single threshold period, a scale out event occurs (unless it occurs within a cool down period).
  * decrement: choose how many servers to turn off (1, 2, 4) during a particular scaling event.
  * cpu less than: define the lower limit of utilization. There are percentage fields for each of the metrics you set. If you selected both, you have the operator option to select whether both thresholds must be reached or just one of them. When a server is at the lower limit as defined for a single threshold period within the scale down window, a scale down event occurs.
  * memory less than: define the lower limit of utilization. There are percentage fields for each of the metrics you set. If you selected both, you have the operator option to select whether both thresholds must be reached or just one of them. When a server is at the lower limit as defined for a single threshold period within the scale down window, a scale down event occurs.
  * threshold period: defines the duration the resource must be outside the bounds of min/max usage in order to autoscale (5 minutes, 10 minutes, 15 minutes, 30 minutes).
  * cool down period: defines how long to wait after performing one Autoscale event before considering another (15 minutes, 20 minutes, 30 minutes).

5. Click **save policy**. Your policy is now part of the list of Horizontal Autoscale policies.
   ![Autoscale Policy](../images/createautoscale-new-h-scale-list.png)

6. **Note**: You need to have a load balancer group created and a pool configured in order to move on to the next step.
   * Please follow the instructions for [creating and configuring a load balancer](../Network/creating-a-self-service-load-balancing-configuration.md) before proceeding.
   * Ideally, you should configure the load balancer with all the IP addresses (and ports) of the servers in the group you plan on applying the autoscale policy to.
   * If you don't do this, the autoscale policy takes care of it for you, but it could take up to five minutes before the load balancer configuration is updated after the autoscale policy is applied to the group.
   * Once you have a load balancer group and pool created and ready to use, continue on to the next steps.

### Configuring Horizontal Autoscale - Existing Server
1. Horizontal Autoscale policies are added to existing server groups, not during the group creation process. Go to the Group Settings page for the group where you want to apply an Autoscale policy. (This group should have at least two identically configured servers in it. See the [Autoscale FAQ](autoscale-faq.md) for more information on how to manage horizontal Autoscale groups of servers.)

2. Click the **Horizontal Autoscale** tab and click **select a horizontal autoscale policy** to show the available policies to select.

3. Select the name of the policy you created in the above steps and choose the load balancer you created in Step 6 along with the port on the servers that the load balancer should route traffic to (or optionally select "none" if you do not need load balancing).

4. Click **save policy**.
   * You may want to read the Autoscale FAQ for more information on how the load balancer options here are used and other additional information.

5. You now see the details of your policy listed to show you that it has been applied to the group.
   ![Group Autoscale Policy](../images/createautoscale-h-policy-details.png)

6. To test your policy, start by creating a significant amount of load on the powered on servers in the group in order to exceed the upper threshold for a sustained period of time.
   * In the example below, see that only two of four servers are currently powered on and we are using the **stress** tool in Linux to simulate very high CPU utilization on the servers.

   ![2 Powered On](../images/createautoscale-two-powered-on.png)
   ![Linux Stress](../images/createautoscale-linux-stress-top.png)

7. After the threshold period has passed, note that there is an additional server in the group that is now powered on and an entry was added to the activity history. The activity history entry shows the average utilization amount that triggered the Autoscale and which servers were affected.

   ![3 Powered On](../images/createautoscale-three-servers-powered-on.png)
   ![Activity Log](../images/createautoscale-activity-log-h-scale-event.png)

8. After the peak usage has dropped below the lower threshold, wait until the cool down period has passed, and watch for a scale in to occur.

   ![2 Powered On](../images/createautoscale-two-powered-on.png)
   ![Activity Log](../images/createautoscale-activity-log-h-scale-in-event.png)

9. Autoscale policies can be changed at any time and those changes are instantly reflected on the related servers. In addition, at any point you can remove the policy from a group. All the servers remain in the group and in the load balancer pool, but none are powered on/off automatically.
