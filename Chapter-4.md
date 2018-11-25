## ৪. আমাদের প্রথম প্লাগইন 

### ৪.১ - প্লাগইন ডেভেলপমেন্ট পরিচিতি

এই ক্লাসে ওয়ার্ডপ্রেসের প্লাগইন কি, প্লাগইন ডেভেলপমেন্টের জন্য কি কি জানতে হবে, প্রভৃতি বিভিন্ন টপিকের উপরে আলোকপাত করা হয়েছে। 

ওয়ার্ডপ্রেস প্লাগইন দিয়ে ওয়ার্ডপ্রেসের বিদ্যমান ফিচারগুলো ইমপ্রুভ করা, এমনকি বাদ দেয়া, বা নতুন ফিচার ডেভেলপ করা যায়। এই ফিচার অ্যাডমিন প্যানেল বা ফ্রন্টএন্ড - দুই জায়গাতেই হতে পারে।

প্লাগইন ডেভেলপমেন্টের সময় কোডিং কনভেনশনের স্বাধীনতা আছে, অর্থাৎ কিনা, ওবজেক্ট-ওরিয়েন্টেড (object-oriented) অথবা প্রসিডিউরাল (procedural) - দুইভাবে প্লাগইন ডেভেলপমেন্টের কোডিং করা যায়।

## ৪.২ - প্লাগইন লেখার বেসিকস এবং আমাদের প্রথম প্লাগইন 

এই ক্লাসে একটি ছোট প্লাগইন ডেভেলপ করা হয়। এটি কোন পোস্টের ভিতরে কতগুলো শব্দ আছে, তা গণনা করে দেখাবে।

প্লাগইন ডেভেলপ করতে হলে প্রথমেই wp-contents/plugins ফোল্ডারের মধ্যে নতুন প্লাগইনের জন্য আলাদা একটি ফোল্ডার তৈরী করতে হবে। এক্ষেত্রে, word-count নামে একটি ফোল্ডার তৈরী করব। ফোল্ডারের মধ্যে word-count.php নামে একটি ফাইল তৈরী করব।

word-count.php ফাইলের উপরে প্লাগইনের একটি ডিক্লারেশন ব্লক লিখতে হবে। এই ব্লকটি ওয়ার্ডপ্রেস "read" করে প্লাগইনের বিস্তারিত তথ্য পেয়ে থাকে।

```php
/*
Plugin Name: Word Count
Plugin URI: https://www.hellobanglaworld.com/
Description: Count words from any WordPress post
Version: 1.0
Author: Manzur Ahmed
Author URI: https://www.github.com/manzurahmed
License: GPLv2 or later
Text Domain: word-count
Domain Path: /languages/
*/
```

প্লাগইনে plugins_loaded হুকের মাধ্যমে প্লাগইনের language ফাইলটি লোড করে নেয়া হয়

```php
function wordcount_load_textdomain() {
	load_plugin_textdomain( "word-count", false, dirname(__FILE__)."/languages" );
}
add_action( "plugins_loaded" , "wordcount_load_textdomain" );
```

এবার, একটা ফিল্টারের দিয়ে the_content কে নিয়ে কাজ করব।

‌‌```php
function wordcount_count_words( $content ) {
	$stripped_content = strip_tags( $content );
	$wordn = str_word_count( $stripped_content );
	$label = __( 'Total Number of Words', 'word-count' );
	$content .= sprintf( '<h2>%s: %s</h2>', $label, $wordn );

	return $content;
}
add_filter( "the_content", "wordcount_count_words" );
```

আমাদের এই প্লাগইনে সব কিছুই হার্ড-কোড করে দেয়া আছে। ক্লায়েন্ট এই প্লাগইনের কোন কিছু পরিবর্তন করতে চাইলে প্লাগইনের কোডে এসে পরিবর্তন করতে হবে। পরবর্তী ক্লাসগুলো দেখানো হবে, কিভাবে প্লাগইনের ভিতরে ক্লায়েন্টের কাজের সুবিধার জন্য কিভাবে ইউজার ইনপুট নেয়ার ব্যবস্থা করা যায়। অ্যাডমিন প্যানেলে ক্লায়েন্ট যে স্ট্রিং ব্যবহার করবে, সেটাই ফ্রন্টএন্ডে দেখা যাবে।
