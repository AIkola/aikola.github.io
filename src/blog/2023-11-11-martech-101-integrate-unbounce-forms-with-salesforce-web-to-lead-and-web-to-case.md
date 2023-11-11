---
title: "Martech 101: Integrate Unbounce forms with Salesforce Web-to-Lead and
  Web-to-Case"
description: A low code approach to integrating Unbounce forms with Salesforce
  Leads and Cases. No additional tools or licenses needed.
author: Antton Ikola
date: 2022-02-14T13:16:55.225Z
tags:
  - post
image: /assets/blog/unbounce-sf-edited.webp
imageAlt: This guide applies to both, web2 Lead and Case.
---
The rise of Low/No Code platforms, such as Salesforce and Unbounce, has increased the amount of exposure of technical work needed from digital marketers. The platforms enable marketers to do more than before, but there are still some gaps that require marketers to ask their developer.

If a growth hacking team does not have a dedicated developer, simple things such as getting leads from a new landing page can halt for months, because of the development teams overburdened backlog. Therefore, it makes sense for a marketing technologist / operations person to know a few tricks when it comes to simple HTML endpoints and passing data.

## Goal and use cases

My main goal in this article is to go through a simple step-by-step guide on how you, as a technical marketer, can integrate an Unbounce landing page to a Salesforce Web-To-Lead or Web-To-Case functionality.

![integrations](/assets/blog/unbounce-sf-edited.webp "Focus of this article: Getting Leads and Case data flowing from Unbounce to SF with minimal code.")

No, you do not need to use Zapier, nor upgrade your Unbounce to a pricing plan that includes Salesforce Integration.

Typical use cases for this could involve experimenting with a new way to acquire leads to Sales on an Unbounce Landing page, or providing customers a new channel to get in contact with Customer Service.

This article focuses purely on technical implementation and omits the obvious parts inherent to the complete steps involved in setting up a new touchpoint, such as designing the landing page and form contents, and deciding on what happens to new Leads and Cases after successful submission.

## Pre-requisites and skills needed

To get going, you need access to two systems:

1. Unbounce: to create a new or edit an existing landing page with an Unbounce form

2. Salesforce: to generate a Web-to-Lead or Web-to-Case HTML

**Skills you need**

1. Basic use of Unbounce

2. Rough understanding on how to read generated “Web-to”-HTMLs

Not too much? Don't worry, we'll get it going.

## 1. Generate a new Web-to-Lead HTML in Salesforce

First, navigate to Salesforce Setup and search “web” in the quick search box. In this example we are running through Web-to-Lead. So Click on Web-to-Lead and then the button “Create Web-to-Lead Form”

(In case you need Web-to-Case, first Enable Web-to-Case and possible auto-responder email under the title and then go to HTML generator which looks similar as Web-to-Lead Setup below.)

![](/assets/blog/salesforce-web-to-lead-setup.webp)

From the Available fields on left, pick and choose any standard or custom Lead fields you want to include in the form. In the example we’ll go with the typical Firs Name, Last Name, Email, Company and Phone.

After you are happy with the fields, click Generate. You are presented the HTML of the form. Copy the code to your favorite code editor.

![](/assets/blog/salesforce-web-to-lead-setup-html.webp)

(Note: I have censored the “oid” value from the HTML. I am using a SF Developer organisation / playground in the example)

**Sidenote**: What you could do, of course, is to just copy and paste that Web-to-Lead HTML to a Unbounce Custom HTML component. The end result of just doing this is not. First of all you are not able to see the form in the landing page builder:

![](/assets/blog/screenshot-2022-02-14-at-12.56.02.webp)

And secondly, the raw HTML of a Web-to-Lead is rendered with system styles, as it is missing any CSS. Without code, a marketer can not do much.

![raw form](/assets/blog/screenshot-2022-02-14-at-12.54.14.webp)

So what we want to do instead, is to utilise the Unbounce form builder, where a marketer can adjust styles without using a single line of code or HTML / CSS markup.

![](/assets/blog/test-lead-unbounce.webp)



## 2. Set up the Confirmation URL endpoint in Unbounce

So now, let’s get back to looking at the Web-to-Lead HTML. First thing we want to find is the endpoint URL where Web-to-Lead is accepting form data. On row 13 we can see the form action endpoint which we will need to set up on Unbounce.

<form action="**https://webto.salesforce.com/servlet/servlet.WebToLead?encoding=UTF-8**" method="POST">

![](/assets/blog/unbounce-form-settings.webp)

Once you click a form in Unbounce, you’ll see the Form Confirmation settings. Select “Post data to a URL” and then paste the form action url **https://webto.salesforce.com/servlet/servlet.WebToLead?encoding=UTF-8** to the URL-box. Set Target as “Same tab or window”.

You do not need to select the “Append form data to URL”, as the POST of the form data will take place in the HTML-header, not in the URL structure.

![](/assets/blog/screenshot-2022-02-14-at-14.07.27.webp)

## 3. Configuring form fields in Unbounce

What we need to do next is to map the Unbounce form fields to the fields generated in the Web-to-Lead HTML. Double-click your form in Unbounce to open the Design Your Form dialog.

![](/assets/blog/screenshot-2022-02-14-at-12.53.18.webp)

Let’s first tackle the rows 15 & 16 from our Web-to-Lead HTML. Here, again, I have replaced my actual oid with a placeholder value=”**xxxxxxxxxxxxxxx**“.

<input type=hidden name="oid" value="**xxxxxxxxxxxxxxx**">

<input type=hidden name="retURL" value="**http://example.com**"

Breaking it apart:

**“oid” value,** the Salesforce Organisation ID where the form values will be forwarded. As you might have noticed, the Web-to-Lead endpoint URL is generic, and is used across all Salesforce organisations. To create a new lead in your org, a correct “oid” value is absolutely mandatory. “retURL” value specifies, where user should be redirected once the form data is submitted successfully

To provide this value in the form submission to the endpoint, we need to provide it as a hidden field with default value. In the Design Your Form window, drag a Hidden field to your form, and edit it with the following rule of thumb: the Field name and ID should match exactly string in parenthesis. For the hidden values oid and retURL, we need to set a default value, provided in the HTML. Copy and paste the correct values in the Field configuration.

![](/assets/blog/unbounce-form-settings-2.webp)

Once you have done similar setup for retURL, we can go forward to setting up the rest of the fields. As an example, let’s take the First Name field in HTML.

<label for="first_name">First Name</label><input  id="first_name" maxlength="40" name="first_name" size="20" type="text" /><br>

Again, we need to be mindful when configuring Unbounce fields, of the string in name=”first_name“

Remember to set up the fields as mandatory, in case you have made any fields required in Salesforce Lead on system level. By default, Lead should have at least Last name, Company and Lead Status information on save operation.

Also, take advantage of the input validation that Unbounce offers. For example, for Phone Numbers, utilise the Phone Number Pre-defined field type instead Text Field and set it to Validate the input.

## 4. Testing and final thoughts

After you are done with the field mapping, go ahead and hit Preview and start testing.

![](/assets/blog/test-lead-unbounce.webp)

In case you do not see a successful test submission in Salesforce, typical issues are usually result of the following things:

1. Organisation ID is not correctly provided in Unbounce for a hidden field and/or there is no Field Name and ID “oid”

2. Form validation is enforced in Unbounce for the fields which are mandatory for Salesforce Lead

3. Form Confirmation Target-setting is set to Parent Frame instead Same tab or window

![](/assets/blog/screenshot-2022-02-14-at-14.07.10.webp)

A successful test!

A further iteration of the integration could include passing utm-parameters as hidden fields to Salesforce, setting default Lead source to “Unbounce” and implementing Google Analytics conversion tracking on the form.

If you have any questions, feel free to connect and start a discussion over [LinkedIn](https://www.linkedin.com/in/anttonikola/).