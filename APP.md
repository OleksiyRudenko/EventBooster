`[2016-08-17] - created`

`[2016-08-17] - latest major update`

# Application

## Table of Contents

1. [Application Structure](#application-structure)
    1. [Public Page](#public-page)
    1. [Management Page](#management-page)
1. [Data Model](#data-model)
    1. [Entities](#entities)
1. [Business Logic](#business-logic)
    1. [Public: Attendee Related Behaviour](#public-attendee-related-behaviour)
    1. [Public: Back-End Data Related Behaviour](#public-back-end-data-related-behaviour)
    1. [Private: Management](#private-management)

## Application Structure

### Public Page

Sections:
 * Cover
 * Targeting
 * Workshop Modules
 * Choice of Bonus Modules
 * Promise to Answer Any Questions
 * Facts: When, Where, Vacant Seats, Cost; Map
 * Testaments [optional]
 * Photo Gallery from Earlier Events [optional]
 * Author
 * Subscription / Registration

[**[back-to-top](#table-of-contents)**]

### Global Management Page
 * Events: add, suspend, assign Host, [re]assign Admin
 * Venue
 * Individuals: add, status, assign to a Host (allow them as Invitees)

### Event Management Page

Provides access to back-end: analysis, actions.

Sections:
 * Event
 * `V`, `W`, `X`, `Y`, `Z`
 * Date milestones
 * Stats & prognosis daily graph:
    - per individual.Type: in, type transition, balance;
    - sales averages: avg(S); avg(-7); avg(-5); avg(-2); avg(-1); avg(Normal); avg(Late)
    - count() prognosis
    - prognosis on
 * Pricing
 * Income analysis & prognosis
 * Event phase
 * Actions:
    - ?
    - ?
 * Individuals listings
    (filter&|orderBy: individuals not involved yet, Invitees|Subscribers|...; name; e-mail; telNr)
    - Per entry actions:
        - ?
        - Reconcile payments
    - List actions:
        - send email

[**[back-to-top](#table-of-contents)**]

## Data Model

### Entities

**Individual**
 * name
 * language
 * telephone
 * email
 * socialProfileFB
 * socialProfileG+
 * isConfirmed
 * isSubscribed
 * unsubscribeToken

**Users**
 * *Individual
 * isServiceAdmin
 * isEventHost
 * isEventAdmin
 * isEventSpeaker

**Speaker-Host** // shared speakers pool
 * *Individual as EventSpeaker
 * *Individual as EventHost

**Prospect-Host** // shared Invitees pool
 * *Individual as Prospect
 * *Individual as EventHost

**Venue**
 * name
 * addressCity
 * addressStreet
 * addressFloor
 * googleMapCoordinates

**Room**
 * *Venue
 * name
 * capacityPax
 * price[asof,duration]

**Event**
 * title
 * baseUrl
 * languages
 * dateOn
 * timeStart
 * duration
 * isConfirmed
 * costTotal
 * *Venue
 * *EventHost
 * *EventAdmin
 * capacityTotal
 * capacityLastCall -- _equals `capacityTotal*0.9` or `capacityTotal-3`_
 * attendee-ReferralsLimit
 * attendee-EarlyBird-Until
 * attendee-EarlyBird-Price
 * attendee-EarlyBird-ReferralRefund
 * attendee-EarlyBird-ReferralDiscount
 * attendee-Normal-Until
 * attendee-Normal-Price
 * attendee-Normal-ReferralRefund -- _must be lesser than for previous stage_
 * attendee-Normal-ReferralDiscount -- _must be lesser than for previous stage_
 * attendee-Late-Until
 * attendee-Late-Price
 * attendee-Late-ReferralRefund -- _must be even lesser than for previous stage_
 * attendee-Late-ReferralDiscount -- _must be even lesser than for previous stage_
 * attendee-OnSite-Price
 * extraSubscribers -- _calculate overVacancies to tell people how many are willing to attend next event_

```
Actual minimum revenue from an attendee =
    Price - FriendDiscount - 3 * max(ReferralDiscount)
```

**Workshop-Attendee**
 * *Workshop
 * *Attendee
 * isSubscribed -- _attendee may terminate further reminders and/or unsubscribe globally_
 * referralCode
 * friendsPaid
 * sumToRefundForReferral -- _from initial Workshop::attendee-FriendsDiscountLimit friends_
 * dateInvited
 * dateRegistered
 * datePmntInstructionsSent
 * dateBasicUntilNotified
 * dateLateUntilNotified
 * dateLastCallNotified
 * datePaid
 * sumPaid
 * isBalanceAligned -- _goto SendTicket()_
 * dateTicketSent

**Token**
 * *Attendee
 * *Workshop
 * token
 * forAction
 * isUsed

**ActionLog**
 * *Individual
 * dateTime
 * *Event
 * action

[**[back-to-top](#table-of-contents)**]

## Business Logic

### Public: Attendee Related Behaviour

```
bttnCallToAction = isSetCookie(registered)
    ? 'actionUpdateMyData-or-RegisterAnotherAttendee'
    : 'actionRegisterMe'

on actionUpdateMyData-or-RegisterAnotherAttendee {
        bttnCallToAction = actionRegisterMe
        UnsetCookie(registered)
        reparse sectionBonusModules.show
        reparse sectionSubscription.show
    }

sectionBonusModules.show = isSetCookie(registered)
    ? CheckboxesHide
    : CheckboxesShow

sectionSubscription.show = isSetCookie(registered)
    ? ThankYou + bttnCallToAction + bttnContactMe
    : Register

```

```
On actionRegisterMe {
    if isSetCookie(registered).email == input.email
        SubmitAndDo(emailConfirmDataUpdate) =
            Thank you! You will be asked to cofirm update by email.
    else
        SubmitAndDo(emailRegister) =
            Thank you! You will be asked to confirm your email and provided with pmnt istructions.
    SetCookie(registered)=input.email
}

On actionContactMe
    SubmitAndDo(emailContactOnRequest) =
        We shall contact you by email first
```

[**[back-to-top](#table-of-contents)**]

### Public: Back-End Data Related Behaviour

```
Initialization config
    WorkshopId = ? // also submitted along with user submissions
    ReferralCode = ? // extracted from url and submitted along with user submissions

Initialization from back-end data
    sectionLocation.*
    sectionSubscribe.*
```

```
if (WorkshopVacancies == 0) {
    sectionLocation.Seats = No vacancies. Please subscribe for next workshop announcement
    sectionLocation.Price = Mark deleted
    bttnCallToAction = actionSubscribeForNextEvent
    }

On actionSubscribeForNextEvent
    SubmitAndDo(emailSubscribe)
        Thank you. We've run out of vacancies. We shall inform you on next workshop date.
        BTW, we have already ... pax subscribed for the next workshop.
```

[**[back-to-top](#table-of-contents)**]

### Private: Management


[**[back-to-top](#table-of-contents)**]