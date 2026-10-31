# Learning Practice: Black-Box Testing, Part 2

## Topics

- Decision table testing
- State transition testing

## Learning Objectives

- Build a decision table.
- Identify conditions and actions.
- Build test cases from a decision table.
- Identify application states, events, and transitions.
- Create a state transition table.
- Build test cases that cover transitions and events.

## Project Requirements

Black-box testing involves designing tests without examining the application's internal code. For this Learning Practice, you will not receive application code. Instead, you will review the following requirements and design test cases based on the application's expected behavior.

### Application

**Name:** North Vacation Booking

**Purpose:** An application that allows users to list their homes as vacation rentals and allows guests to make reservations.

### Reservation Requirements

- A reservation must be between 1 and 31 nights, inclusive.
- A reservation must include between 1 and 12 guests, inclusive.
- The reservation costs $200 per night.
- Every valid reservation includes a one-time $50 cleaning fee.
- Each guest over five costs an additional $25 per night.
- The check-in date must be before the checkout date.
- The number of nights is calculated as `checkout date - check-in date`.
- The checkout date is not counted as a reserved night.
- Every night in the requested date range must be available.
- Invalid reservation information raises a `ValueError`.
- An unavailable reservation raises an `UnavailableError`.
- A new valid reservation begins in the `Pending` state.
- Successful payment moves it from `Pending` to `Confirmed`.
- Failed payment moves it from `Pending` to `Cancelled`.
- A confirmed reservation can move to `Checked In` on its check-in date.
- A checked-in reservation moves to `Completed` after checkout.
- A pending or confirmed reservation may be cancelled.
- A cancelled or completed reservation cannot change states.
- Invalid state transitions raise an `InvalidStateError`.

## Resources

- [Decision Table Testing — GeeksforGeeks](https://www.geeksforgeeks.org/software-engineering/decision-table-based-testing-in-software-testing/)
- [State Transition Testing — GeeksforGeeks](https://www.geeksforgeeks.org/software-engineering/state-transition-testing/)

## Step 1: Identify Conditions and Actions

Review the reservation requirements and identify:

- Five conditions that affect whether a reservation is accepted.
- Five actions that may occur based on those conditions.

<details>
<summary>Examples</summary>

### Condition Examples

- The check-in and checkout dates are valid.
- The reservation is between 1 and 31 nights.
- The reservation includes between 1 and 12 guests.
- Every requested night is available.
- The reservation includes more than five guests.

### Action Examples

- Create the reservation.
- Calculate the standard reservation price.
- Add the extra guest fee.
- Raise a `ValueError`.
- Raise an `UnavailableError`.

</details>

## Step 2: Create a Decision Table

Create a decision table containing at least six relevant combinations of conditions and their expected actions.

Use:

- `T` for true
- `F` for false
- `-` when the condition does not affect the result
- `X` when an action should occur

Your table should follow this structure:

| Conditions and Actions         | Rule 1 | Rule 2 | Rule 3 | Rule 4 | Rule 5 | Rule 6 |
| ------------------------------ | :----: | :----: | :----: | :----: | :----: | :----: |
| Dates are valid                |        |        |        |        |        |        |
| Number of nights is valid      |        |        |        |        |        |        |
| Number of guests is valid      |        |        |        |        |        |        |
| Requested nights are available |        |        |        |        |        |        |
| More than five guests          |        |        |        |        |        |        |
| **Create reservation**         |        |        |        |        |        |        |
| **Calculate standard price**   |        |        |        |        |        |        |
| **Add extra guest fee**        |        |        |        |        |        |        |
| **Raise `ValueError`**         |        |        |        |        |        |        |
| **Raise `UnavailableError`**   |        |        |        |        |        |        |

<details>
<summary>Example table</summary>

| Conditions and Actions             | Rule 1 | Rule 2 |
| ---------------------------------- | :----: | :----: |
| Valid check-in and checkout dates? |   T    |   T    |
| Between 1 and 31 nights?           |   T    |   T    |
| Between 1 and 12 guests?           |   T    |   T    |
| Every requested night available?   |   T    |   T    |
| More than five guests?             |   F    |   T    |
| **Create reservation**             |   X    |   X    |
| **Calculate standard price**       |   X    |   X    |
| **Add extra guest fee**            |        |   X    |
| **Raise `ValueError`**             |        |        |
| **Raise `UnavailableError`**       |        |        |

- **Rule 1:** Create a valid reservation for five or fewer guests without an extra guest fee.
- **Rule 2:** Create a valid reservation for more than five guests and add the extra guest fee.

</details>

## Step 3: Create Decision Table Test Cases

Create at least one test case for every rule in your decision table. If your table contains six rules, you must create at least six test cases.

Each test case must include:

- Test-case ID
- Conditions
- Input values
- Expected action
- Expected price or exception

<details>
<summary>Example test case</summary>

### TC01: Valid Reservation With No Extra Guest Fee

- **Check-in:** October 1
- **Checkout:** October 4
- **Number of guests:** 3
- **Availability:** Every requested night is available.
- **Expected Result:** The reservation is created.
- **Expected Price:** `$650`
  - Three nights at $200: `$600`
  - One-time cleaning fee: `$50`

</details>

## Step 4: Identify Reservation States and Events

- Identify five possible reservation states.
- Identify five events that may cause the reservation to change states.

<details>
<summary>Examples</summary>

### Reservation States

- `Pending`
- `Confirmed`
- `Checked In`
- `Completed`
- `Cancelled`

### Events That Change Reservation States

- Payment succeeds
- Payment fails
- Guest cancels
- Guest checks in
- Guest checks out

</details>

## Step 5: Create a State Transition Table

Document the expected result of each event from each relevant state.

Your table must include at least:

- Six valid state transitions
- Three invalid state transitions
- Nine transitions total

Use the following structure:

| Current State | Event            | Expected State | Expected Result          |
| ------------- | ---------------- | -------------- | ------------------------ |
| Pending       | Payment succeeds | Confirmed      | Reservation is confirmed |
|               |                  |                |                          |

<details>
<summary>Example table</summary>

| Current State | Event                      | Expected State | Expected Result                                          |
| ------------- | -------------------------- | -------------- | -------------------------------------------------------- |
| Pending       | Payment succeeds           | Confirmed      | The reservation is confirmed.                            |
| Pending       | Payment fails              | Cancelled      | The reservation is cancelled and the dates are released. |
| Confirmed     | Guest checks in            | Checked In     | The guest is checked in.                                 |
| Checked In    | Guest checks out           | Completed      | The reservation is completed.                            |
| Confirmed     | Guest cancels              | Cancelled      | The reservation is cancelled and the dates are released. |
| Cancelled     | Guest attempts to check in | Cancelled      | An `InvalidStateError` is raised.                        |
| Completed     | Guest attempts to cancel   | Completed      | An `InvalidStateError` is raised.                        |

</details>

## Step 6: Create State Transition Test Cases

Create test cases that cover:

- Every valid state transition, for a minimum of six test cases
- At least three invalid state transitions
- At least one complete reservation sequence
- At least one cancellation sequence

You must create at least 11 state transition test cases in total.

<details> 
<summary>Example table</summary>

| Test ID | Starting State | Event                      | Expected State | Expected Result                   | Transition Type |
| ------- | -------------- | -------------------------- | -------------- | --------------------------------- | --------------- |
| ST01    | Pending        | Payment succeeds           | Confirmed      | The reservation is confirmed.     | Valid           |
| ST02    | Cancelled      | Guest attempts to check in | Cancelled      | An `InvalidStateError` is raised. | Invalid         |

</details>

## What to Submit

1. Create and initialize a GitHub repository.
2. Commit and push your work to the repository.
3. Make sure the repository is public. Private repositories will not receive credit.
4. Submit the repository URL on Canvas.

## AI Transparency

AI was used in the creation of this file.

# Learning Practice: Black-Box Testing, Part 2

**Application:** North Vacation Booking
**Techniques:** Decision table testing, state transition testing

## Assumptions

- Invalid input is checked before availability, so invalid input raises `ValueError` even if the dates would also be unavailable.
- Price = (nights x $200) + $50 cleaning fee + (extra guest fee, if more than five guests).
- Extra guest fee = (guests - 5) x $25 x nights.
- A "failed" event on an invalid transition leaves the reservation in its current state.
- A guest can only check in on the check-in date. Trying earlier or later is treated as an invalid transition (`InvalidStateError`). This is my interpretation of the requirements and should be confirmed.

---

## Step 1: Conditions and Actions

### Conditions

1. C1: Check-in and checkout dates are valid (check-in is before checkout).
2. C2: The reservation is between 1 and 31 nights.
3. C3: The reservation includes between 1 and 12 guests.
4. C4: Every requested night is available.
5. C5: The reservation includes more than five guests.

### Actions

1. A1: Create the reservation.
2. A2: Calculate the standard reservation price.
3. A3: Add the extra guest fee.
4. A4: Raise a `ValueError`.
5. A5: Raise an `UnavailableError`.

---

## Step 2: Decision Table

| Conditions and Actions         | Rule 1 | Rule 2 | Rule 3 | Rule 4 | Rule 5 | Rule 6 |
| ------------------------------ | :----: | :----: | :----: | :----: | :----: | :----: |
| Dates are valid                |   T    |   T    |   F    |   T    |   T    |   T    |
| Number of nights is valid      |   T    |   T    |   -    |   F    |   T    |   T    |
| Number of guests is valid      |   T    |   T    |   -    |   -    |   F    |   T    |
| Requested nights are available |   T    |   T    |   -    |   -    |   -    |   F    |
| More than five guests          |   F    |   T    |   -    |   -    |   -    |   -    |
| **Create reservation**         |   X    |   X    |        |        |        |        |
| **Calculate standard price**   |   X    |   X    |        |        |        |        |
| **Add extra guest fee**        |        |   X    |        |        |        |        |
| **Raise `ValueError`**         |        |        |   X    |   X    |   X    |        |
| **Raise `UnavailableError`**   |        |        |        |        |        |   X    |

- **Rule 1:** Valid reservation, five or fewer guests, no extra fee.
- **Rule 2:** Valid reservation, more than five guests, extra fee added.
- **Rule 3:** Invalid dates raise `ValueError`.
- **Rule 4:** Valid dates but nights outside 1-31 raise `ValueError`.
- **Rule 5:** Valid dates and nights but guests outside 1-12 raise `ValueError`.
- **Rule 6:** Valid input but at least one night unavailable raises `UnavailableError`.

---

## Step 3: Decision Table Test Cases

| ID   | Rule | Conditions | Input values | Expected action | Expected price / exception |
| ---- | ---- | ---------- | ------------ | --------------- | -------------------------- |
| TC01 | 1 | All valid, all available, 5 or fewer guests | Check-in Oct 1, checkout Oct 4 (3 nights), 3 guests, all nights available | Create reservation, calculate standard price | `$650` (3 x $200 = $600, + $50 cleaning) |
| TC02 | 2 | All valid, all available, more than 5 guests | Oct 1 to Oct 4 (3 nights), 8 guests, all nights available | Create reservation, calculate standard price, add extra guest fee | `$875` ($600 + $50 + 3 extra guests x $25 x 3 nights = $225) |
| TC03 | 3 | Checkout before check-in | Check-in Oct 4, checkout Oct 1, 3 guests | Raise `ValueError`; no reservation created | `ValueError` |
| TC04 | 3 | Check-in equals checkout (zero nights) | Check-in Oct 1, checkout Oct 1, 3 guests | Raise `ValueError`; no reservation created | `ValueError` |
| TC05 | 4 | Dates valid, 32 nights (above maximum) | Oct 1 to Nov 2 (32 nights), 3 guests | Raise `ValueError`; no reservation created | `ValueError` |
| TC06 | 5 | Dates and nights valid, 0 guests | Oct 1 to Oct 4, 0 guests | Raise `ValueError`; no reservation created | `ValueError` |
| TC07 | 5 | Dates and nights valid, 13 guests (above maximum) | Oct 1 to Oct 4, 13 guests | Raise `ValueError`; no reservation created | `ValueError` |
| TC08 | 6 | Input valid, Oct 2 already booked | Oct 1 to Oct 4, 3 guests, Oct 2 unavailable | Raise `UnavailableError`; no reservation created | `UnavailableError` |
| TC09 | 6 | Input valid, only the last reserved night (Oct 3) unavailable | Oct 1 to Oct 4, 3 guests, Oct 3 unavailable | Raise `UnavailableError` | `UnavailableError` |
| TC10 | 1 | Boundary: minimum nights and guests | Oct 1 to Oct 2 (1 night), 1 guest, available | Create reservation, calculate standard price | `$250` ($200 + $50) |
| TC11 | 1 | Boundary: exactly 5 guests, no extra fee | Oct 1 to Oct 3 (2 nights), 5 guests, available | Create reservation, calculate standard price | `$450` ($400 + $50) |
| TC12 | 2 | Boundary: 6 guests (first extra guest) | Oct 1 to Oct 3 (2 nights), 6 guests, available | Create reservation, calculate standard price, add extra guest fee | `$500` ($400 + $50 + 1 x $25 x 2) |
| TC13 | 2 | Boundary: maximum nights and guests | Oct 1 to Nov 1 (31 nights), 12 guests, available | Create reservation, calculate standard price, add extra guest fee | `$11,675` ($6,200 + $50 + 7 x $25 x 31 = $5,425) |
| TC14 | 1 | Checkout night is not counted: checkout date already booked by someone else | Oct 1 to Oct 4, 3 guests, Oct 4 unavailable (Oct 1-3 available) | Create reservation | `$650` |

---

## Step 4: Reservation States and Events

### States

1. `Pending`
2. `Confirmed`
3. `Checked In`
4. `Completed`
5. `Cancelled`

### Events

1. Payment succeeds
2. Payment fails
3. Guest cancels
4. Guest checks in (on the check-in date)
5. Guest checks out (after checkout)

---

## Step 5: State Transition Table

| # | Current State | Event | Expected State | Expected Result | Type |
| - | ------------- | ----- | -------------- | --------------- | ---- |
| 1 | Pending | Payment succeeds | Confirmed | The reservation is confirmed. | Valid |
| 2 | Pending | Payment fails | Cancelled | The reservation is cancelled and the dates are released. | Valid |
| 3 | Pending | Guest cancels | Cancelled | The reservation is cancelled and the dates are released. | Valid |
| 4 | Confirmed | Guest cancels | Cancelled | The reservation is cancelled and the dates are released. | Valid |
| 5 | Confirmed | Guest checks in (on check-in date) | Checked In | The guest is checked in. | Valid |
| 6 | Checked In | Guest checks out | Completed | The reservation is completed. | Valid |
| 7 | Pending | Guest attempts to check in | Pending | An `InvalidStateError` is raised. | Invalid |
| 8 | Confirmed | Payment succeeds again | Confirmed | An `InvalidStateError` is raised. | Invalid |
| 9 | Checked In | Guest cancels | Checked In | An `InvalidStateError` is raised. | Invalid |
| 10 | Cancelled | Guest attempts to check in | Cancelled | An `InvalidStateError` is raised. | Invalid |
| 11 | Cancelled | Payment succeeds | Cancelled | An `InvalidStateError` is raised. | Invalid |
| 12 | Completed | Guest attempts to cancel | Completed | An `InvalidStateError` is raised. | Invalid |
| 13 | Completed | Guest attempts to check in | Completed | An `InvalidStateError` is raised. | Invalid |

---

## Step 6: State Transition Test Cases

| Test ID | Starting State | Event | Expected State | Expected Result | Transition Type |
| ------- | -------------- | ----- | -------------- | --------------- | --------------- |
| ST01 | Pending | Payment succeeds | Confirmed | The reservation is confirmed. | Valid |
| ST02 | Pending | Payment fails | Cancelled | The reservation is cancelled and the dates are released. | Valid |
| ST03 | Pending | Guest cancels | Cancelled | The reservation is cancelled and the dates are released. | Valid |
| ST04 | Confirmed | Guest cancels | Cancelled | The reservation is cancelled and the dates are released. | Valid |
| ST05 | Confirmed | Guest checks in on the check-in date | Checked In | The guest is checked in. | Valid |
| ST06 | Checked In | Guest checks out after checkout | Completed | The reservation is completed. | Valid |
| ST07 | Pending | Guest attempts to check in | Pending | An `InvalidStateError` is raised. | Invalid |
| ST08 | Confirmed | Payment succeeds again | Confirmed | An `InvalidStateError` is raised. | Invalid |
| ST09 | Checked In | Guest attempts to cancel | Checked In | An `InvalidStateError` is raised. | Invalid |
| ST10 | Cancelled | Guest attempts to check in | Cancelled | An `InvalidStateError` is raised. | Invalid |
| ST11 | Completed | Guest attempts to cancel | Completed | An `InvalidStateError` is raised. | Invalid |
| ST12 | Cancelled | Payment succeeds | Cancelled | An `InvalidStateError` is raised. | Invalid |
| ST13 | Completed | Guest attempts to check in | Completed | An `InvalidStateError` is raised. | Invalid |
| ST14 | Confirmed | Guest attempts to check in before the check-in date (assumption) | Confirmed | An `InvalidStateError` is raised. | Invalid |

### Complete reservation sequence

| Test ID | Step | Starting State | Event | Expected State | Expected Result |
| ------- | ---- | -------------- | ----- | -------------- | --------------- |
| ST15 | 1 | Pending (new valid reservation) | Payment succeeds | Confirmed | The reservation is confirmed. |
| ST15 | 2 | Confirmed | Guest checks in on check-in date | Checked In | The guest is checked in. |
| ST15 | 3 | Checked In | Guest checks out | Completed | The reservation is completed. |
| ST15 | 4 | Completed | Guest attempts to cancel | Completed | An `InvalidStateError` is raised. |

### Cancellation sequences

| Test ID | Step | Starting State | Event | Expected State | Expected Result |
| ------- | ---- | -------------- | ----- | -------------- | --------------- |
| ST16 | 1 | Pending (new valid reservation) | Payment succeeds | Confirmed | The reservation is confirmed. |
| ST16 | 2 | Confirmed | Guest cancels | Cancelled | The reservation is cancelled and the dates are released. |
| ST16 | 3 | Cancelled | Guest attempts to check in | Cancelled | An `InvalidStateError` is raised. |
| ST17 | 1 | Pending (new valid reservation) | Payment fails | Cancelled | The reservation is cancelled and the dates are released. |
| ST17 | 2 | Cancelled | Payment succeeds | Cancelled | An `InvalidStateError` is raised. |
| ST17 | 3 | Cancelled | Guest cancels | Cancelled | An `InvalidStateError` is raised. |

---