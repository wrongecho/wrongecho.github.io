## UniFi Switch - SNMP Monitoring issue

Recently, I ran into an issue where my new UniFi Switch wasn't responding to SNMP Monitoring. This was odd as I already had the community string configured in the management console and working fine with my access points.

After some research, the solution was [this](https://community.ui.com/questions/UniFi-Switch-SNMP/f66e3528-ce97-4c12-840e-056870c13b68#answer/57be62be-857f-42c6-8acb-e96b97db9fa6) 6 year old post on the UniFi forum.

Seemingly, I had a 20 character community string that worked fine with APs however this still does not work with a Switch. Changing the v2 community string to 11 characters resolved the issue for me.
