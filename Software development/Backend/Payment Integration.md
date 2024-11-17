We need to account first to get this shit. So it doesn't work for now.
### Stripe
![[Pasted image 20241030221724.png]]
- First we use the Stripe UI to setup UI in React which is in docs.
- We send the amount to backend.
- Backend create `payment_intent` route and there we intend to pay and return `client_secret` to frontend. This `client_secret` is crucial because it allows the frontend to securely confirm the payment with Stripe.
```
app.post("/create-payment-intent", async (req, res) => {
  const { amount } = req.body;
  try {
    const paymentIntent = await stripe.paymentIntents.create({
      amount: amount,
      currency: "usd",
    });
    res.send({ clientSecret: paymentIntent.client_secret });
  } catch (error) {
    res.status(500).send({ error: error.message });
  }
});
```
- The frontend gets the `client_secret` from the backend and then uses **Stripe.js** to **confirm the payment** using the card details provided by the user. This action sends the card details (securely) to Stripe, and Stripe processes the payment. If the card is valid and funds are available, Stripe approves the payment and returns a `paymentIntent` with a status of `"succeeded"`. If there’s an issue with the card (e.g., insufficient funds or incorrect details), Stripe will return an error message that the frontend can handle and display to the user.
- Optionally, the backend can receive **webhook events** from Stripe to keep track of the status of payments. For example, Stripe sends a webhook when the payment succeeds or fails.

#### Paypal