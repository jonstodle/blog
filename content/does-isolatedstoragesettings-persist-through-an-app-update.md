+++
date = "2012-07-13T10:00:00.0000000+00:00"
tags = ["Windows Phone"]
title = "Does IsolatedStorageSettings persist through an app update?"

+++
This was a question I ended up asking myself as I was about to push my first update to Quote List. My first thought was “Yes”, that seems the logical answer. The data that I put there are persistent through application launches and I’ve made sure the code to handle this data checks whether there’s data stored already, to prevent overwriting existing data. As it turns out, the updating of an app doesn’t touch the data stored in IsolatedStorageSettings. So, as long as you’ve written proper code that checks for existing data and prevents the app to overwrite said data if it’s present, there’s no worry.

Of course this is the expected and probable behavior, but the fact was; I couldn’t find anyone actually confirming this on the web. So my goal with this (rather short) post is to aid the next aspiring developer who wonders the same thing: Does `IsolatedStorageSettings` persist through an app update?