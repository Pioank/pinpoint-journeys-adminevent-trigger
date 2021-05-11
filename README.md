# pinpoint-journeys-adminevent-trigger
Triggering a Pinpoint Journey based on a company wide event e.g. new feature release and reaching out to multiple customers

## Use cases:
* Automate new product release communications to all customers
* Automate system updates' communications that take place in the background but customers need to be informed
* Any backend process that customers need to be informed for and emits an event 

## Background: 
Companies strive to build personalized user experiences, thus many marketing communication tools place the customer at the centre of their features. Companies use customer events on their mobile or web applications to trigger personalised user journeys. However there is also the case where the company might want to initiate a user journey for a segment based on a companywide event such as new feature release.
Amazon Pinpoint's current functionality can send a Campaign or initiate a Journey based on a user event. Though only customers who trigger that event will go through the journey or receive the campaign communications. This functionality does not cover use cases where the company would like to use an event to trigger a Campaign or Journey for a whole segment.

## Solutions:
Depending the use-case the above scenario can be solved in different ways:
* **Pinpoint Journeys & Lambdas:** This approach requires a Pinpoint user with one endpoint that will represent the company. Using this user one can send events to Pinpoint via API calls triggering a Pintpoint event based Journey. Note that the Pinpoint company user should be the only one with access to these events. When the Pinpoint eventbased Journey is triggered, Lambdas can be used to create campaigns on the fly and campaign hooks to apply further filtering.
  * **Prons:** Orchestrate multiple communications using Pinpoint Journeys drag & drop functionality for the Wait and Lambda building blocks. Utilize existing PutEvent Pinpoint API operation to trigger a Pinpoint Journey. Allow business users to create & configure Pinpoint Journeys to a certain degree without coding
  * **Cons:** Initial setup requires coding of Lambdas that are responsible of createing the Campaigns and Cmapaign hooks. Any segment, segment criteria, communications’ content changes will require coding since all that information will reside in Lambdas
* **Amazon Step Functions:** AWS Step Functions allows you to orchestrate Amazon Lambdas in which you can call Pinpoint APIs for create Segment, Campaign and Journey. The Step Function created will be triggered from the event you specify such as new feature release
  * **Prons:** Automated process of consuming the event and sending out the communications
  * **Cons:** All building and future changes require coding. Business users will not be able to perform any changes without the help of a developer
* **Pinpoint Campaign / Journey, scheduled or immediate execution:** You can manually setup a Pinpoint Campaign or Journey and schedule it for when that event will occur or execute it immediately. This approach is manual and it scaling would result to a linear increase of time required and risk of errors.
  * **Prons:** Intuitive UI to create segments, Campaigns & Journeys
  * **Cons:** Manually triggering the Campaign(s), Journey(s) every time the relevant event occurs

## Approach No1 important points:
* **How to update user attributes based on event attributes:** When firing the event (server side or client side) also update the user attributes of the company user that is firing the event using the UpdateEndpoint API operation. That way the event attributes will be accessible within the Pinpoint Journey payload. A Lambda Pinpoint Journey building block can create a Pinpoint campaign with a campaign hook that will update conditionally or not users' attributes within the specified segment.
* **How to add Yes/No condition for a segment:** Since all communications to customers are being send by Lambdas, the Lambda code should create as many Pinpoint Campaigns as required depending the conditions e.g. customers who belong to segment A will receive email-A and customers who belong to segment B will receive email-B. In that case the Lambda should create two Pinpoint Campaigns where each of them would send email-A to segment A and email-B to segment-B
* **Use customers’ communication interaction events such as email delivered:** This approach cannot obtain and use customers’ communication interaction events to dictate next Pinpoint Journey steps

## Example architecture
![alt text](https://github.com/Pioank/pinpoint-journeys-adminevent-trigger/blob/main/admin-event-architecture.JPG)
