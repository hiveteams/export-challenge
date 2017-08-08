# Hive Export App Challenge
### A few notes
This exercise is meant to test a few core competencies and is based on real problems we've worked with in the past. As a result, the exercise is intentionally open-ended to allow for creative problem solving and encourage question asking.

We work primarily with Meteor (node, mongo and [Blaze](https://guide.meteor.com/blaze.html)), but you are free to use whatever tools you need to get the job done.

### Problem Statement
This is your first day at Hive, and you're excited to start building tools to make everyone's working life easier. One customer support rep in particular is having a tough week. He's being asked over and over again by customers for data exports of all "Action" (tasks in Hive) information for compliance and reporting. The best he can do right now is ask an engineer to go to the production database and send him JSON files of the data requested, which he then copies and pastes into CSV formatted files. He's losing sleep doing such tedious tasks, and apparently other CS reps are getting requests like this more and more.

One of the members of the product team enlists you to help solve this tedious problem. Your goal will be to create a portal for "admins" (Hive team members) to log into and access downloadable exports for any given customer from. You've also been given the amazing freedom to name the internal product something punny and bee-related, but no pressure :)

#### The data
You've been given a selective database dump with a few sample users, workspaces, projects and actions. The database dump is for a mongo database, so you'll need to set up a local version of it and connect your application to it - this will be switched out when it goes into production. In Hive, `workspaces` are the highest hierarchal unit and contain `projects` and `actions`. The `actions` collection is meant to make up the rows of each CSV export. Each action row should also include denormalized fields rather than their ID (for example, show the name of the project instead of its id on a given action). In Hive, `actions` can be infinitely nested; a single action can have child actions, grandchild actions, and so on. The CSV export should group actions by both the project they belong to (if any) and the action's parents/grandparents/however far up.

A sample `action` data structure looks something like:
```
{
  _id: 'xyz',
  title: 'To do',
  projectId: 'someProjectId',
  parent: null,
  root: null,
  workspace: 'someWorkspaceId',
  assignees: ['userId1', 'userId2'],
  labels: ['labelId1'],
  ...
}
```
Where `action.parent` points to its immediate parent, and `action.root` points to its highest up ancestor.

A sample `project` data structure might look something like:
```
{
  _id: 'def',
  name: 'My project',
  workspace: 'someWorkspaceId',
  ...
}
```
And finally a sample `workspace`:
```
{
  _id: 'hij',
  name: 'Some Workspace',
  members: ['userId1', 'userId2'],
  ...
}
```

A given CSV export contains *all* of the action information for a given workspace, including deleted and archived items.

### What you need to do

**Task 1 is required. A choice of either Task 2 or Task 3 is required, but not both.**


#### Task 1 (required): Create a single page web application for the CSV export requests portal. Guidelines:
1. Only authorized personnel should have access to the admin area, so these accounts can be standalone and not tied to any other system.
2. Logged in users should be able to create a new "data export" request, which asks for a given `workspace` id and the fields of the "Actions" they would like to export.
3. All data exports should be stored with information including the user who requested it, the workspace it was requested for, and the time it was submitted.
4. Once a request is submitted, the user should see a loader while the request is processed.
5. Once finished processing, the user should see a link to download the CSV export from.
6. Since CS reps are often on the go (at customer meetings), the site needs to be accessible and usable from any device.
7. Because this product will most likely be customer facing in the future, the UI should be clean and attractive. Keep it lightweight and *do not use a css framework* (Bootstrap, Foundation, etc.)

**Note: The sample data sets are small compared to some of the ones in production. Once live, this app will need to parse through sets that are 10,000+ documents. Be sure it is performant.**

Your exporting app has been live for a few weeks, and the team is loving it. It's saving each rep 4+ hours a week of miserable copy/pasting. But there are a few things that could improve the workflow slightly. Since reps are on the go so often, the process of downloading a CSV and then emailing it to the customer becomes somewhat frustrating. They all agree that if they could both save exports for later and email the exports directly from the portal, it'd be a much better process.

#### Task 2 (option 1): Allow access to old exports and sending of exports to email addresses. Guidelines:
1. Upon login, users should see a reverse-chronological feed of all the requested exports, including the workspace id, time of request and email of requester
2. Each export should have an option after the CSV link that allows the user to enter an email address
3. Upon entry of an email address, the application should send an email to that address with a copy of the CSV. Since some of these emails are customer-facing, the subject and body should explain what it is.

The CS reps can't stop buzzing about the time you save them every week. The CEO has noticed and prompts the product team to consider including this as a public facing app for Hive users. Before continuing, they want to get some usage reports - but since it was early on in your days at Hive, you had no chance to set up analytics! Luckily, you were smart enough to set up logs on every export request and can use those to get the data that the product team is after.

#### Task 3 (option 2): Write a basic script to gather usage data. Guidelines:
1. Compute the total number of export requests since launch
2. Compute the number of requests per unique workspace
3. Compute the most frequent requester, as they might have valuable feedback about the product
4. Here are some sample log outputs:
  1. [ 2017-07-27T18:58:18+00:00 ] requester email = john@hive.com, workspace = ABC123
  2. [ 2017-07-27T18:59:53+00:00 ] requester email = jd@hive.com, workspace = DEF456

Note: You're able to download the logs as plain text files and run the script on your machine to show the results to the product team.
