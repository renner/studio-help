# Migrating from SUSE Gallery to Studio

---
### Upgrading Older Base Systems

Some of the appliances have older base systems. It might be easier to first upgrade
them to the most recent version and *then* do the export / import.

---

If you found an appliance on SUSE Gallery and you want to build and use it
in your SUSE Studio Onsite server, use the following procedure:

1. Go to [SUSE Gallery](http://susestudio.com/browse) and log in.
2. Select your preferred appliance.
3. Click the _Clone appliance..._ link on the right side.
4. Select appropriate image type in the _Build_ tab. (Note that only specific image type data for this image type will be stored in the resulting Kiwi export archive).
5. Export tarball (click the _Export your appliance's Kiwi configuration_ link).
6. Import tarball on your SUSE Studio onsite server.


Limitations:

* Some of the published appliances may have a package conflict. 
This is likely caused by the changes in the repositories or packages of these
appliances.
