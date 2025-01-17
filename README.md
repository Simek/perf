# Can you get this code to work?

I can't. So far, no one can. Maybe you will be the one?

### HPCs via ReadThreadProfilingData would be extremely useful.

In theory, [KeSetHardwareCounterConfiguration](https://docs.microsoft.com/en-us/windows-hardware/drivers/ddi/ntddk/nf-ntddk-kesethardwarecounterconfiguration) is supposed to allow a user-mode application (ie., not a kernel-mode driver) to request monitoring of the hardware performance counters on x64 processors. After calling KeSetHardwareCounterConfiguration, a thread is supposed to be able to call [EnableThreadProfiling](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-enablethreadprofiling) and then periodically read the hardware performance counters with [ReadThreadProfilingData](https://docs.microsoft.com/en-us/windows/win32/api/winbase/nf-winbase-readthreadprofilingdata).

This would be incredibly useful for developers, because it would allow them to get continuous, accurate hardware performance counter data in just one simple periodic call, without the huge headache that is [Event Tracing for Windows](https://docs.microsoft.com/en-us/windows/win32/etw/about-event-tracing).

### However, nobody seems to know how to get it to work.

This sample code shows how it _should_ theoretically work, at least according to the documentation. However, despite all the API calls returning successful result codes, no hardware performance counter data is ever provided: the HwCountersCount field is always 0, and all entries of the HwCounters array are also 0.

So far, I've tried:

* Running in an administrator shell, just to see if permissions matter
* Randomly picking indexes to pass to KeSetHardwareCounterConfiguration
* Setting the KeSetHardwareCounterConfiguration indexes to known indexes reported from **wpr -pmcsources**
* Passing different bitmasks to EnableThreadProfiling (0xf, 0xffff, etc.)
* Passing and/or not passing READ_THREAD_PROFILING_FLAG_DISPATCHING

but to no avail. I've yet to find any magic combination of API modifications that makes it work.

### A request for documentation was filed, but unfortunatley Microsoft closed it without adding any.

You can find it [https://github.com/MicrosoftDocs/windows-driver-docs-ddi/issues/1039](here). It sounds like perhaps the documentation team doesn't actually know how to make it work either, so it may require a kernel dev or someone with similarly deep knowledge of the internals to explain what's going wrong.

One possibility is that this API was never actually hooked up to the performance counters for some reason. If that's the case, it would be fantastic if someone could put that on a to-do list somewhere. This API is exactly what you would want for retrieving these numbers: extremely simple, direct, and real-time. It'd allow everyone to add in-line profiling of HPCs with just a few calls, in a standard way, without needing to install any weird kernel drivers. 

And that would be great! 

\- Casey
