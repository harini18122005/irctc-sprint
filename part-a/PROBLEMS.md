# IRCTC Problem Discovery — Part A

## Summary
- Total problems documented: 6 (3 given + 3 self-discovered)
- Platform explored: irctc.co.in live site
- Device used: desktop Chrome on Windows
- Exploration date: 2026-06-18

---

## Problem 1: Tatkal Booking Crashes at 10:00 AM [Given]

**What is broken:**
Tatkal booking has no reliable progress feedback when demand spikes at release time. Users reach the booking flow, but the platform does not show queue position, meaningful loading state, or a clear failure message when the booking attempt stalls or fails. The user cannot tell whether the site is still working, overloaded, or already out of quota.

**Affected users:**
High-intent Tatkal travelers, especially commuters and last-minute passengers who must book exactly at release time. This is a nationwide failure mode because Tatkal inventory is concentrated into a narrow morning window and the site is used by millions of users at the same moment.

**Frequency:**
Daily at roughly 10:00 AM for Tatkal windows. The failure is recurring and predictable because the demand spike happens at the same time every day.

**Current flow — step by step:**
1. User opens IRCTC around 9:50 AM and prepares the journey details.
2. User signs in or stays ready on the booking/search screen.
3. User selects Tatkal-oriented settings and keeps the page open waiting for release.
4. User refreshes or triggers the search right at 10:00 AM.
5. The page either stalls, slows down, or returns a generic result without telling the user what is happening.
6. The user keeps waiting because there is no visible queue position or progress indicator.
7. The user finally learns that Tatkal quota is gone only after a failed attempt, timeout, or blank/noisy feedback.

**Where exactly it breaks:**
Step 5. The system fails to communicate state during overload. The user is left without a queue, status, or actionable error, so the booking attempt becomes guesswork.

---

## Problem 2: Search Filters Do Not Work Reliably [Given]

**What is broken:**
The train-list filter panel is dense and state-heavy, and changing filters does not feel reliably reflected in the listing. The interface requires scrolling inside a crowded sidebar, and the result list does not clearly confirm what changed after each filter action.

**Affected users:**
Regular train search users comparing classes, departure windows, and availability across routes. This hits both first-time and repeat travelers because filtering is a core task before booking.

**Frequency:**
Very common whenever users compare trains, especially on busy routes where they refine results repeatedly.

**Current flow — step by step:**
1. User opens IRCTC and searches a route between two major stations.
2. Search results load with a large filter panel on the side.
3. User tries to narrow by Sleeper class, available seats, or a morning departure window.
4. The filter controls are crowded and partially buried below the fold.
5. The user applies or toggles filters and expects the result list to update immediately and obviously.
6. The listing does not give strong feedback about what changed, so the user cannot easily tell whether the filter was applied.
7. When the user goes back or repeats the change, the prior state is not clearly preserved or explained.

**Where exactly it breaks:**
Step 6. The filtering system fails the visibility-of-state test. The page does not clearly show that the result set changed, which makes the filter behavior feel unreliable even when controls are present.

---

## Problem 3: Seat Selection Resets [Given]

**What is broken:**
Seat preference appears unstable during the booking handoff. A user can reach seat/berth selection, choose a lower berth, and then lose that preference when proceeding to passenger details. This creates a mismatch between what the user selected and what the next screen shows.

**Affected users:**
Passengers with berth preference, especially older travelers, families, and anyone who specifically needs a lower berth for comfort or mobility reasons. On mobile, the issue is reported to happen more often because the booking flow is more compressed.

**Frequency:**
Common in active booking flows, and more likely on mobile because the interface is tighter and the booking handoff has less room for error.

**Current flow — step by step:**
1. User searches a train and opens the booking flow.
2. User reaches the seat map or berth selection step.
3. User selects a lower berth.
4. User clicks Proceed to continue booking.
5. The passenger-details page opens.
6. The previously chosen berth is no longer visible or is not clearly preserved.
7. The user must re-check or re-enter the selection, risking a mismatch before payment.

**Where exactly it breaks:**
Step 5. The booking handoff does not reliably carry seat preference into the next screen, so the user’s selection state gets lost or becomes unclear.

---

## Problem 4: AskDisha Chat Obscures the Booking Form [Self-discovered]

**How I found it:**
On the live train-search screen, while preparing a route from New Delhi to Mumbai Central, I saw the floating AskDisha widget overlap the main form on the right side of the page.

**Screenshot or description:**
The top booking form sits under a large floating AskDisha button and tooltip. The widget occupies the same visual area as the main input section and competes with the primary task before the user even starts typing.

**What is broken:**
A persistent chatbot overlay competes with the actual train-search flow. It covers part of the form, adds visual noise, and pulls attention away from the primary task of booking a ticket.

**Affected users:**
Desktop users starting a search, especially new or low-confidence users who rely on the primary form layout to orient themselves.

**Frequency:**
Always present on the home/train-search entry screen, so it affects a large share of first interactions.

**Current flow — step by step:**
1. User opens IRCTC to book a train.
2. The page loads with the main search form.
3. A floating AskDisha widget appears over the right side of the form.
4. The user tries to read or click the form fields.
5. The overlay competes with the booking controls and adds clutter.
6. The user must visually work around the widget before continuing.

**Where exactly it breaks:**
Step 3. The helper element is too prominent too early. It interferes with task focus before the user has even started the booking workflow.

---

## Problem 5: Search Results Expose Too Much Filter Complexity [Self-discovered]

**How I found it:**
After searching New Delhi to Mumbai Central on the live site, I inspected the results panel and found a very large filter stack covering class, train type, departure time, arrival time, from stations, and to stations all at once.

**Screenshot or description:**
The results screen shows multiple expanded filter groups in one dense left sidebar. The first train card is visible on the right, but the filters dominate the page and push the actual result list down and out of attention.

**What is broken:**
The train-list screen overwhelms users with too many nested filters and too little hierarchy. It is difficult to understand which filter should be used first, and the screen does not guide the user toward the fastest path to an actionable result.

**Affected users:**
Comparison shoppers, occasional travelers, and mobile users who need to make quick choices from crowded routes.

**Frequency:**
Every time a route returns multiple trains, which is most of the time on major city pairs.

**Current flow — step by step:**
1. User searches for a common route.
2. The results page loads with one or more train cards.
3. A large filter sidebar appears with multiple expanded groups.
4. The user must scan several sections before understanding how to narrow results.
5. The result list competes visually with the filter stack.
6. The user spends time parsing the interface instead of choosing a train.

**Where exactly it breaks:**
Step 3. The interface overloads the user with too many controls at once, so the search result page stops feeling like a decision aid and starts feeling like a dashboard.

---

## Problem 6: IRCTC Loads with Heavy Third-Party Noise [Self-discovered]

**How I found it:**
While using the live site, the browser console repeatedly showed blocked ad and analytics requests, plus mixed-content warnings from image resources while the train-search page loaded.

**Screenshot or description:**
The page still loads, but the browser reports repeated CSP, mixed-content, and ad-network errors. The interface also triggers a lot of background activity unrelated to ticket booking.

**What is broken:**
The site depends on a noisy third-party stack that creates visible browser warnings and request failures during normal use. Even when the booking UI works, the loading experience feels unstable and cluttered.

**Affected users:**
Everyone who opens the site, but especially users on slower connections, older browsers, or devices where any extra request failure adds latency and confusion.

**Frequency:**
Observed continuously during a single live session and likely present on every page load because the same scripts and banners are reloaded each time.

**Current flow — step by step:**
1. User opens IRCTC in a browser.
2. The page begins loading core booking controls.
3. Multiple ad and analytics requests fire in the background.
4. The browser logs CSP, mixed-content, and blocked-request warnings.
5. The user sees the booking page only after this noisy load sequence.
6. The experience feels slower and less trustworthy than the booking task should feel.

**Where exactly it breaks:**
Step 3. Non-essential third-party resources compete with the core booking experience and create avoidable noise during the most important screen load.
