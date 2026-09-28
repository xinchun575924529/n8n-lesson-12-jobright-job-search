# Production hardening checklist

If you deploy this as a real service (e.g. a public "job finder" page on your site):

1. **Uptime probe**: schedule a tiny monitor workflow that calls the endpoint every hour and alerts
   (Feishu/Telegram/email) on 2 consecutive failures. The endpoint has no SLA.
2. **Rate limit the form**: n8n forms are public; add a captcha (form node option) or front the
   form with a CDN/WAF rule. Each submission costs one live API call.
3. **Cache identical searches**: a simple 15-minute cache keyed on (title, location, preference)
   cuts API calls and improves response time dramatically.
4. **Log structured results**: persist `jobList` snapshots (file/DB) so you can diff availability
   and prove the service works over time.
5. **Keep the error net**: never remove `onError: continueErrorOutput`; extend "Explain search
   failure" with a timestamp and a support link.
6. **Watch for contract drift**: the Validate node intentionally throws when `success != true` or
   `jobList` is not an array - that is your early-warning system. Alert on it, do not silence it.
7. **Version pin**: if you self-host n8n, record that the patched body mode (JSON) was verified on
   2.33.3; re-run the L2-C workflow after upgrades.