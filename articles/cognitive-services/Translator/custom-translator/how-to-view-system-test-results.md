---
title: View system test results and deployment - Custom Translator
titleSuffix: Azure Cognitive Services
description: When your training is successful, review system tests to analyze your training results. If you're satisfied with the training results, place a deployment request for the trained model.
author: rajdeep-in
manager: christw
ms.service: cognitive-services
ms.component: custom-translator
ms.date: 11/13/2018
ms.author: v-rada
ms.topic: article
#Customer intent: As a Custom Translator user, I want to understand how to view system test results, so that I can review test results and analyze my training.
---

# View system test results

When your training is successful, review system tests to analyze your training results. If you're satisfied with the training results, place a deployment request for the trained model. Go to specific project, go to models tab of that project, then go to appropriate model and select test tab.

The test tab shows you:

1.  System Test Results: The result of the test process in the trainings. The test process produces the BLEU score.

    -  Sentence Count: How many parallel sentences were used in the test set

    -  BLEU Score: BLEU score generated for a model after training completion

    -  Status: If the test process is complete or in progress

        ![System test results](media/how-to/how-to-system-test-results.png)

2.  Click on the System test results, and that will take you to test result details page. This page shows the machine translation of sentences that were part of the test dataset.

3.  The table on the test result details page has two columns - one for each
    language in the pair. The column for the source language shows the sentence
    to be translated. The column for the target language contains two sentences
    in each row.

    -  Ref: This sentence is the reference translation of the source sentence as given in the test dataset.

    -  MT: This sentence is the automatic translation of the source sentence done by the model built after the training was conducted.

        ![System test results compare](media/how-to/how-to-system-test-results-2.png)


## Download test

Click the Download Translations link to download a zip file. The zip contains the
machine translations of source sentences in the test data set.

![Download test](media/how-to/how-to-system-test-download.png)

This downloaded zip archive contains three files.

1.  custom.mt.txt: This file contains machine translations of source language sentences in
    the target language done by the model trained with user’s data.

2.  ref.txt: This file contains user provided translations of source language sentences in
    the target language.

3.  Src_Dataset_source.txt: This file contains sentences in the source language.

    ![Downloaded system test results](media/how-to/how-to-download-system-test.png)


## Deploy a model

To request a deployment:

1.  Click on the project name

2.  In the project page, go to Models tab.

    -  For a successfully trained model, it shows “Deploy” button, if not deployed.

        ![Deploy model](media/how-to/how-to-deploy-model.png)

3.  Click on Deploy. You can view the status of your deployrequest in the “Status” column.

[!Note] If a model is deployed already, you'll see “Undeploy” button for that model. To undeploy a model click "Undeploy" button. 