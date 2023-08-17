# Elevating FOFA Search Efficiency through Novel Approaches

## Introduction
In the process of using FOFA search, filtering conditions through aggregated information on result pages often proves to be incredibly useful, allowing swift addition of relevant criteria. However, whether it's the feedback from FOFA engineers or my own experience while streamlining assets, I've encountered significant challenges!

FID (FeatureID) is the fingerprint information created by FOFA itself, and the long string of characters it comprises lacks any inherent reference value. I'd need to open a new page to understand what category of websites a particular FID represents and whether it pertains to the target website I need. This significantly extends the time I spend organizing assets. Moreover, aggregation information only supports addition; exclusion isn't directly possible. When I click, it automatically adds "&&" (AND), and if I find that it doesn't match what I need, I have to exclude that condition, repeating the same process. I'm determined to become a 'mouse monkey' but excessive clicking is frustrating!

This led me to a new consideration: How can I maximize the efficiency of filtering through aggregated statistics, especially for understanding time-consuming information like FID?

## Feature Implementation
We attempted a preview operation for FID fingerprints. By clicking a button, a pop-up window opens, conducting a second aggregation of the FID fingerprints aggregated on the result page. I can then preview all the information to judge the FID result, promptly adding or filtering as needed.

Good news: The feature is implemented! Bad news: The response time is measured in minutes... What use is that? I could have completed it using the previous method in that time.

This led to a second thought: What scenarios and objectives do I need to address?

I need to preview the types of websites associated with this FID to quickly determine if I need this feature. Do I need to view all its aggregated features? What characteristics can assist my judgment efficiently?

We redefined requirements and final objectives. We selected features that facilitate quick judgment: website titles, main domain names, and IP ranges. We aggregated and displayed the Top 2 samples, which perfectly met my need for swift judgments.

Enough talk – let's see how this feature works!

![Efficiency GIF 1](https://github.com/FofaInfo/Awesome-FOFA/blob/c0652fc6ae2d11816f6ff8a74318eaf4a7d07af0/Storage/effiency1.gif)

View ➡️ Select Multiple ➡️ Exclude in One Click – What do you need to type on the keyboard? Nothing! Quickly exclude site cluster data.

Moreover, the other feature is even simpler. We've directly added buttons for multiple selections, enabling easy addition or exclusion. Just click the mouse. Like this:

![Efficiency GIF 2](https://github.com/FofaInfo/Awesome-FOFA/blob/c0652fc6ae2d11816f6ff8a74318eaf4a7d07af0/Storage/effiency2.gif)

## Conclusion
Understanding users, thinking like users, and striving to assist users in solving issues to improve their experience with our product have always been our main objectives. During feature implementation, we encountered challenges and took some detours. However, once we clarified the business goals and reconsidered the problems, we realized that nothing is insurmountable. In fact, sometimes the simplest methods are the most effective technological solutions. This perseverance and rethinking are the driving forces behind our journey.