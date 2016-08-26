`[2016-08-17] - created`

`[2016-08-17] - latest major update`

# Event Process

[**[Documentation Root](../README.md)**]

## Table of Contents

 1. [Process Overview](#process-overview)
 1. [Definitions](#definitions)
 1. [Workflow Model](#workflow-model)
    1. [Initialization](#initialization)
    1. [Survey](#survey)
    1. [Sales Campaign](#sales-campaign)
    1. [Postponement](#postponement)
    1. [Cancellation](#cancellation)
    1. [D-Day](#d-day)
    1. [Post Event](#post-event)
 1. [Pricing](#pricing)
 1. [Email Actions Reference](#email-actions-reference)

## Overview

One may want to run a probably recurring Workshop. It might be reasonable to run a survey prior
to any principal activities. The prospects database shall be used if any. Any respondents become
prospects. Any involved individual may reject further communications on the promoted event as well
as on any future events. However, any interest restores individual as a prospect.

Pricing differentiation can be applied (EarlyBird, Normal, Late, OnSite/NightOwl).

Promo codes can be used to boost sales. Promo codes can be published by attendees only.
Promo code provides discount for limited/unlimited number of attendee's affiliates.
Attendee can benefit from acquiring his/her affiliates. However, limited number of those
earn benefit for the attendee (normally 3 only). And benefit can be used to pay for
future events.

PostEvent phase allows attendees to download materials, provide feedback.

Prospects and Attendees can do general inquiries or request for refund.

Attendees may obtain vouchers on cash discounts for future events.

The process in general described below. Please, refer to other parts of documentation
to learn about IT support provided.

[**[back-to-top](#table-of-contents)**]

## Definitions

**Individual**

In global context:
 * Attendee - prospect
 * Refugee - explicitly rejected any further communications; reset when registers to any Event
 * Speaker - speaker
 * Host - hosts events
 * Admin (to a Host) - administrates Host's events

In context of Event:
 * Invitee - invited by host to visit landing page/registration
 * Subscriber - subscribed for event launch notice
 * Registered - registered while not paid yet
 * Pending - didn't reject promo and payment reminders 
   but `Z` has been reached before individual became Attendee
 * Attendee - admittance paid
 * Graduate - attended event
 * Skipper - paid but didn't attend event

Individual may also reject any further promo and payment reminder
emailing 
(set noPresalesReminder property), which doesn't affect status advancement 
(e.g. Registered->Attendee)
and any other communications
(payment/registration confirmation, tickets, stop-sales notice etc.)

User states and transitions inbetween:

![User States Diagram](http://g.gravizo.com/g?
digraph G {
    aize ="4,4";
    User -> Invitee [label="Invitation sent"];
    Invitee -> Subscriber [label="Subscribed for news"];
    Subscriber -> Registered [label="Registered for attendance"];
    Registered -> Attendee [label="Admittance paid"];
    Attendee -> Graduate [label="Attended"];
    Attendee -> Skipper [label="Not attended",color=red];
    Registered -> Pending [label="Z count reached",style=dotted];
  }
)

Attendees Count Aliases:
- `V` - absolute required minimum count(Attendees)
- `W` - pre-last call threshold
- `X` - optimal count(Attendees)
- `Y` - last call threshold
- `Z` - maximum count(Attendees)

**Dates/Milestones**
 * Day `D` - Day of Event
 * Day `S` - Day of Sales Campaign launch

All date calculations consider business days. E.g. if day `D` is Tuesday then day `D-2`
is a precedent Friday.

**Sales KPI**
 * `avg(-x)` - average daily sales over last `x` days
 * `avg(S)` - average daily sales since day `S`
 * `avg(Normal)` - average daily sales since Normal sales phase start
 * `avg(Late)` - average daily sales since Late sales phase start

[**[back-to-top](#table-of-contents)**]

## Workflow Model

!!!TODO: Confirm subscription/registration

Phases:
 * Initialization
 * Survey - reveal interest if any
 * Sales Campaign
 * Sales Boost - invoked when sales progress is poor
 * Postponement - invoked when sales target achievable but more time required
 * Cancellation - invoked when sales target obviously cannot be achieved in reasonable
    time scope
 * Sales Closed - invoked when sales target achieved before day D
 * D-Day - on day D
 * PostEvent - on day after day D

### Initialization
 * Define Host, Admin
 * Define re Event: topic and components, location(city), duration,
    { `U`, `W`, `X`, `Y`, `Z`}, external resources (web-site, page on social network)
 * Add Invitees to the Event context
    
### Survey
 * Define Survey questions
 * Make a landing page (cover, targeting, modules, extras, testimonials/gallery,
    {when,where,seats,price}.proposed, author, `subscribe` = `subscribe.survey|surveyNotice`)
    - Survey: contacts (name, email, telephone, city), checkbox (would attend in my city),
        date options
    - Survey form pre-populated with data from cookies/browser-input-history/email-url/DB
    - If isSubscriber then `subscribe.surveyNotice` = thank you; click if you want to
        change anything -> `subscribe.survey`; share
    - On submission: Invitee => Subscriber
 * Publish on social networks / venues sites
 * Invitee: Email invitation: url+clientid, [remindLater,] discontinueThis, discontinueAll
 * Collect Subscribers:
    - email confirmation: thank you, on city-mismatch -> discontinueThisCity
    - notify Host
    
### Sales Campaign
 * Define re Event: date and time, location, prices
 * Create event notices on social networks / venues sites
 * Update Landing Page: ... refs to public events, `subscribe` = `subscribe.register`
    - If isRegistered then `subscribe` = `subscribe.registerNotice`
        (amend, register another attendee)
 * On Register
    - If `price.salesPhase==0` || `price.user==0`
        - Invitee|Subscriber => Attendee: thankyou, ticket, share, url.calendarEntry
    - Otherwise: Invitee|Subscriber => Registered: payment instructions, contactMe, [referral-code promise]
    - Other individuals: you've been registered already
 * Collect payments
    - Registered => Attendee: Email pmnt confirmation: thankyou, ticket,
        share, url.calendarEntry

count(Attendee) Milestones:
 * ~~On `V`:~~
 * On `W`:
    - Invitee|Subscribed|Registered: optimum group is nearly formed, `W/days_since_S`
        are sold daily, discontinueThis|All
    - Registered: + pmnt instructions, contactMe
 * On `X`:
    - Invitee|Subscribed|Registered: Hurry!: group is formed, still `Z-X` seats remain,
        `X/days_since_S` are sold daily, discontinueThis|All
    - Registered: + pmnt instructions, contactMe
 * On `Y`:
    - Invitee|Subscribed|Registered: Last call: `Z-Y` seats remain, discontinueThis|All
    - Registered: + pmnt instructions, contactMe
 * On `Z`:
    - goto **#Sales-Closed**

Timeline Milestones:
 * On day `S`:
    - Invitee: Email invitation: details, prices, url+clientid, [remindLater,] discontinueThis, discontinueThisCity, discontinueAll
    - Subscriber: Email good news: details, prices, url+clientid, [remindLater,] discontinueThis, discontinueAll
 * On day `salesNormal-2` (two days of EarlyBird yet):
    - Invitee|Subscriber: Email price: 2 days before price raise, discontinueThis|All
    - Registered: Email price: 2 days before price raise, contactMe
 * On day `salesLate-2`:
    - Invitee|Subscriber: Email price: 2 days before price raise, discontinueThis|All
    - Registered: Email price: 2 days before price raise, contactMe
 * On day `D-5`:
    - If count(Attendees) less than
        - `V-avg(-7)*5`:
            - Attendee: invite friends, get voucher, promo-code
            - Registered: pay and get voucher
            - Subscriber: register to get special offer -> pay and get voucher
        - `X-avg(-7)*5`: _possibly sales boost techniques_
        - `Z-avg(-7)*5`: _possibly sales boost techniques_
 * On day `D-2`:
    - If count(Attendees) less than
        - `V-avg(-7)*7`: goto **#Cancellation**
        - `V-avg(-7)*2`: invoke **#Postponement**; retry end-of-day
        - `X-avg(-3)*2`: _possibly sales boost techniques_
        - `Z-avg(-3)*2`: _possibly sales boost techniques_
    - Invitee|Subscriber|Registered: Last call: 2 days before price raise, discontinueThis|All
    - Registered: + contactMe
 * On day `D-1`:
    - If count(Attendees) less than
        - `V-avg(-3)`: goto **#Cancellation**
        - `X-avg(-3)`: _possibly sales boost techniques_
        - `Z-avg(-3)`: _possibly sales boost techniques_
    - If count(Attendees) > `V`: reminder

### Postponement

If there is resource among Subscribed|Registered to achieve targets:
 * Registered:
    - we have count(Attendees); still `V-count(Attendees)` seats vacant; unlikely that we make
      it till Day D; but if you make a decision right now, you could save the Event;
      we grant you a discount to make it easier for you to decide + promo-code for your friends
      (extra per-friend vouchers) who will get discounts and you earn extra voucher;
    - please, decide: pay or url.discontinueThis
 * Subscribed:
    - _same as above_ but also url.getRegistered

If no resource among Registered to achieve targets:
 * Web-site:
    - update date, add 'POSTPONED' banner
    - If isAttendee: url-contactMe for refund if you wish
 * Subscriber|Registered: there still `n` attendees required to run the Event
    (currently we have ...); trend shows we need extra `m` days to get those;
     therefore we have to postpone 1 week; if you're OK with that and really want to
     participate, please, register and pay
 * Attendee: + refund instruction, voucher option + discount

### Cancellation
 * Web-site:
    - `buttonAction` = Keep Updated
    - `subscribe` = `subscribe.keepUpdated`
 * Subscriber|Registered: unfortunately, we have to cancel; if you still interested in the topic
    then url+clientid
 * Attendee: + refund instruction, voucher option + discount
 **FINAL STATE**

### Sales Closed
 * Web-site:
    - add banner 'WE ARE COMPLETE for this time'
    - `buttonAction` = is!Attendee: Keep Updated
    - `subscribe` = `subscribe.keepUpdated`
 * Invitee|Subscribed|Registered: we are complete, we'll keep you posted
 * Registered: + please, do not pay; if you've already done but haven't received
    confirmation yet, please, url.contactMe
 * Attendee: we are complete!; looking forward to seeing you

### D-Day
 * Web-site:
    - add banner 'WE ARE COMPLETE for this time'
    - `buttonAction` = is!Attendee: Keep Updated
    - `subscribe` = `subscribe.keepUpdated`
 * Attendee: looking forward to seeing you today

### PostEvent
 * Web-site:
    - downloads
    - feedback summary => testaments
    - pictures
    - `survey` = `survey.furtherTopics` + `survey.feedback`
 * Attendee => Graduate|Skipper
 * Graduate : thankyou, url.downloads@clientId
 * Skipper : itsapity, voucher-as-a-compensation
 * Others : see how it's been : url.#Testaments

## Pricing

Sales stages: EarlyBird, Normal, Late, OnSite/NightOwl

```
 Costs = Venue costs + Materials costs + Travel Costs
 MinimalProfit = ?      // minimal profit to earn
 
 ReferralsLimit = ?     // how many refunds provided as a maximum
 ReferralRefund[] = ?   // refund per referral per sales stage (decreases from stage to stage)
 ReferralDiscount[] = ? // discount provided by reference code per sales stage (decreases from stage to stage)
 // See Event entity specification below for details
 
 TicketMinimumPrice = (Costs + MinimalProfit) / U + ReferralDiscount[EarlyBird] + ReferralRefund[EarlyBird] * ReferralsLimit
  
 Example:
        Costs = 2500
        MinimalProfit = 500
        U = 15
        ReferralsLimit = 3
        ReferralRefund[EarlyBird] = 50
        ReferralDiscount[EarlyBird] = 50
    TicketMinumumPrice = (2500 + 500) / 15 + 50 + 50 * 3 = 400
     
```

Offer discount vouchers on Normal and Late stages only. Then `TicketPrice[Normal]`>=`TicketPrice[EarlyBird]`

Event Promotion Management fees options:
 1. We take money from Attendees between `W` and `Y` + 70% of ticket price difference until `Z`
 2. Fixed fee (included into Event costs) + 70% of ticket price difference from between `Y` and `Z`

[**[back-to-top](#table-of-contents)**]

## Email Actions Reference
- discontinueThis = discontinue further emails on this event: *=>Rejector
- discontinueThisCity = as above but for a city mismatch reason: *=>Rejector, store City
- discontinueAll = discontinue all similar emails: *=>Rejector&Refugee
- contactMe = personal contact request: email to confirm telephone. Request types:
    - i've paid but still receive emails as if I wouldn't have had
    - [want money back]
    - other questions
- remindLater = remind later

[**[back-to-top](#table-of-contents)**]