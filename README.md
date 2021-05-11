# pinpoint-journeys-adminevent-trigger
Triggering a Pinpoint Journey based on a company wide event e.g. new feature release and reaching out to multiple customers

## Background: 
Companies strive to build personalized user experiences and communications, thus many marketing communication tools place the customer at the centre of their operations / features. Companies use customer events on their mobile or web applications to trigger personalised user journeys. However there is also the case where the company might want to initiate a user journey for a segment based on a companywide event such as new feature release.
Amazon Pinpoint can send a Campaign or initiate a Journey based on a user event. Though only customers who trigger that event will go through the journey or receive the campaign communications. This functionality does not cover use cases where the company would like to use an event to trigger a Campaign or Journey for a whole segment.

## Solutions:
Depending the use-case the above scenario can be solved in different ways
* **Pinpoint Journeys & Lambdas:** This approach requires a Pinpoint user with one endpoint that will represent the company. Using this user you can send events to Pinpoint server side via API. Setup a Pintpoint Journey that is triggered from a specific event that only the company Pinpoint use has access to. When the Pinpoint Journey is triggered, Lambdas can be used to create campaigns on the fly and campaign hooks to apply further filtering. T
  * **Prons:** Orchestrate multiple communications using Pinpoint Journeys drag & drop functionality. Utilize existing PutEvent Pinpoint API operation to trigger a Pinpoint Journey. Allow business users to create & configure such Pinpoint Journeys to a certain degree without coding
  * **Cons:** Initial setup and coding of Lambdas that will create Campaign is required. Any segment, segment criteria, communications’ content changes will require development support since all that will reside in Lambdas
* **Pinpoint Campaign / Journey, scheduled or immediate execution:** You can manually setup a Pinpoint Campaign or Journey and schedule it for when that event will occur or execute it immediately. This approach is manual and it scaling would result to a linear increase of time required and risk of errors.
  * **Prons:** Intuitive UI to create segments, Campaigns & Journeys
  * **Cons:** Manually triggering the Campaign(s), Journey(s) every time the relevant event occurs
* **Amazon Step Functions:** AWS Step Functions allows you to orchestrate Amazon Lambdas in which you can call Pinpoint APIs for create Segment, Campaign and Journey. The Step Function created will be triggered from the event you specify such as new feature release
  * **Prons:** Automated process of consuming the event and sending out the communications
  * **Cons:** All building and future changes require coding. Business users will not be able to perform any changes without the help of a developer

## Approach No1 important points:
* **How to update user attributes based on event attributes:** When firing the event (server side or client side) also update the user attributes of the admin user that is firing the event using the UpdateEndpoint API operation. That way the event attributes will be accessible within the Pinpoint Journey payload. A Lambda Pinpoint Journey building block can create a Pinpoint campaign with a campaign hook that will update all customers within the specified segment (conditionally or not).
* **How to add Yes/No condition for a segment:** Since all communications to customers are being send by Lambdas, the Lambda code should create as many Pinpoint Campaigns as required depending the conditions e.g. customers who belong to segment A will receive email-A and customers who belong to segment B will receive email-B. In that case the Lambda should create two Pinpoint Campaigns where each of them would send email-A to segment A and email-B to segment-B
* **Use customers’ communication interaction events such as email delivered:** This approach cannot obtain and use customers’ communication interaction events as Pinpoint Journeys does for single customers

