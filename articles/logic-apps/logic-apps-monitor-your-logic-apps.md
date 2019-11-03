---
title: Check run status and view run history - Azure Logic Apps
description: Check your logic app's run status, view run history, and set up alerts in Azure Logic Apps
services: logic-apps
ms.service: logic-apps
ms.suite: integration
author: ecfan
ms.author: estfan
ms.reviewer: divswa, estfan, LADocs
ms.topic: article
ms.date: 11/04/2019
---

# Check run status, view run history, and set up alerts in Azure Logic Apps

After you [create and run a logic app](../logic-apps/quickstart-create-first-logic-app-workflow.md), you can check that logic app's run status, run history, trigger history, and performance. To get notifications about failures or other possible problems, set up [alerts](#add-azure-alerts). For example, you can create an alert that detects "when more than five runs fail in an hour."

For real-time event monitoring and richer debugging, set up diagnostics logging for your logic app by using the [Logic Analytics capability](../azure-monitor/log-query/get-started-portal.md) in [Azure Monitor](../azure-monitor/overview.md). This Azure service helps you monitor your cloud and on-premises environments so that you can more easily maintain their availability and performance. You can then find and view events, such as trigger events, run events, and action events. By storing this information in [Azure Monitor logs](../azure-monitor/platform/data-platform-logs.md), you can create [log queries](../azure-monitor/log-query/log-query-overview.md) that help you find and analyze this information. You can also use this diagnostic data with other Azure services, such as Azure Storage and Azure Event Hubs. For more information, see [Monitor logic apps by using Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md).

[!INCLUDE [azure-monitor-log-analytics-rebrand](../../includes/azure-monitor-log-analytics-rebrand.md)]

## Review runs history

1. In the [Azure portal](https://portal.azure.com), find and open your logic app in the Logic App Designer.

   To find your logic app , in the main Azure search box, enter `logic apps`, and then select **Logic Apps**.

   ![Find and select "Logic Apps" service](./media/logic-apps-monitor-your-logic-apps/find-your-logic-app.png)

   The Azure portal shows all the logic apps that are associated with your Azure subscriptions. You can filter this list based on name, subscription, resource group, location, and so on.

   ![View logic apps associated with subscriptions](./media/logic-apps-monitor-your-logic-apps/logic-apps-list-in-subscription.png)

1. Select your logic app, and then select **Overview**.

   On the overview pane, under **Runs history**, all the past, current, and any waiting runs for your logic app appear, for example:

   ![Overview, runs history, and other logic app information](media/logic-apps-monitor-your-logic-apps/overview-pane-logic-app-details-run-history.png)

   > [!TIP]
   > If you don't find all the data that you expect, on the toolbar, select **Refresh**.

   For status definitions and descriptions, see [Troubleshoot your logic app](../logic-apps/logic-apps-diagnosing-failures.md).

1. To review the steps and other information for a specific run, under **Runs history**, select that run.

   ![Select a specific run to review](media/logic-apps-monitor-your-logic-apps/select-specific-logic-app-run.png)

   The **Logic app run** pane shows each step in the selected run, each step's run status, and the time taken for each step to run, for example:

   ![Each action in the specific run](media/logic-apps-monitor-your-logic-apps/logic-app-run-pane.png)

1. To get more information about a specific step, select that step so that the shape expands. You can now view details such as inputs, outputs, and any errors that happened in that step, for example:

   ![View the details for a specific step](media/logic-apps-monitor-your-logic-apps/specific-step-inputs-outputs-errors.png)

   > [!NOTE]
   > All runtime details and events are encrypted within the Logic Apps service. 
   > They are decrypted only when a user requests to view that data. 
   > You can [hide inputs and outputs in run history](../logic-apps/logic-apps-securing-a-logic-app.md#obfuscate) 
   > or control user access to this information by using 
   > [Azure Role-Based Access Control (RBAC)](../role-based-access-control/overview.md).

1. To get more information about the run itself, on the toolbar, select **Run Details**.

   ![On the toolbar, select "Run Details"](media/logic-apps-monitor-your-logic-apps/select-run-details-on-toolbar.png)

   This information summarizes the steps, status, inputs, and outputs for the run.

   ![Review information about the run](media/logic-apps-monitor-your-logic-apps/review-logic-app-run-details.png)

   For example, you can get the run's **Correlation ID** property, which you might need when you use the 
   [REST API for Logic Apps](https://docs.microsoft.com/rest/api/logic).

## Review trigger history

1. To view all the trigger activity for your logic app, on your logic app's menu, select **Overview**.
In the **Summary** section, under **Evaluation**, select **See trigger history**.

   ![View trigger history for your logic app](media/logic-apps-monitor-your-logic-apps/overview-pane-logic-app-details-trigger-history.png)

1. To view information about a specific trigger event, on the trigger pane, select the specific trigger event that you want to review.

   ![View trigger history for your logic app](media/logic-apps-monitor-your-logic-apps/select-trigger-event-for-review.png)

   You can now review information about the selected trigger event, for example:

   ![View specific trigger information](media/logic-apps-monitor-your-logic-apps/view-specific-trigger-details.png)

<a name="add-azure-alerts"></a>

## Set up monitoring alerts

To set up alerts without [Azure Monitor logs](../log-analytics/log-analytics-overview.md), follow these steps. 

To get alerts based on specific metrics or exceeded thresholds for your logic app, set up [alerts in Azure Monitor](../azure-monitor/platform/alerts-overview.md). Learn about [metrics in Azure](../monitoring-and-diagnostics/monitoring-overview-metrics.md).

For more about creating alerts, see 

1. On your logic app menu, under **Monitoring**, select **Alerts** > **New alert rule**.

   ![Add an alert for your logic app](media/logic-apps-monitor-your-logic-apps/set-up-alerts.png)

1. On the **Create rule** pane, follow these steps:

   1. Under **Resource**, select your logic app, if not already selected. Under **Condition**, select **Add** so that you can define the condition that triggers the alert.

      ![Add a condition for the rule](media/logic-apps-monitor-your-logic-apps/add-condition-for-rule.png)

   1. On the **Configure signal logic** pane, find and select the signal for to get an alert. You can use the search box, or to sort the signals alphabetically, select the **Signal name** column header.

      For example, if you want to send an alert when a trigger fails, follow these steps:

      1. In the **Signal name** column, find and select the **Triggers Failed** signal.

         ![Select signal for creating alert](media/logic-apps-monitor-your-logic-apps/find-and-select-signal.png)

      1. On the information pane that opens for the selected signal, under **Alert logic**, set up your condition, for example:

         1. For **Operator**, select **Greater than or equal to**.

         1. For **Aggregation type**, select **Count**.

         1. For **Threshold value**, enter `1`.

         1. Under **Condition preview**, confirm that your condition appears correct.

         1. Under **Evaluated based on**, set up the interval and frequency for running the alert rule. For **Aggregation granularity (Period)**, select the period for grouping the data. For **Frequency of evaluation**, select how often you want to check the condition.

         1. When you're ready, select **Done**.

         Here's the finished condition:

         ![Set up condition for alert](media/logic-apps-monitor-your-logic-apps/set-up-condition-for-alert.png)

   1. Specify a name, optional description, and severity level for your alert. Either leave the **Enable rule upon creation** setting turned on, or turn off until you're ready to enable the rule.

   1. When you're done, select **Create alert rule**.

> [!TIP]
> To run a logic app from an alert, you can include the 
> [request trigger](../connectors/connectors-native-reqres.md) in your workflow, 
> which lets you perform tasks like these examples:
> 
> * [Post to Slack](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-slack-with-logic-app)
> * [Send a text](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-text-message-with-logic-app)
> * [Add a message to a queue](https://github.com/Azure/azure-quickstart-templates/tree/master/201-alert-to-queue-with-logic-app)

## Next steps

* [Monitor logic apps by using Azure Monitor](../logic-apps/logic-apps-monitor-your-logic-apps-oms.md)
* [Automate logic app deployment](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
* [Monitor B2B messages](../logic-apps/logic-apps-monitor-b2b-message.md)
