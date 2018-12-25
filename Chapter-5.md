# ৫ - আমাদের দ্বিতীয় প্লাগইন

## ৫.১ - পোস্ট এ কিউ আর কোড দেখানোর প্লাগইন - ইন্ট্রোডাকশন

এই পর্বে QR Code দেখানোর পদ্ধতি কিভাবে ডেভেলপ করা যায়, সে সম্পর্কে আলোচনা করা হয়েছে। ডেভেলপমেন্টের মধ্যে এ্যাকশন হুক, ফিল্টার হুক ব্যবহার করা হতে পারে।

পরের ভিডিওতে কোডিং এর মূল পর্বটি দেখানো হবে।

## ৫.২ - পোস্ট এ কিউ আর কোড দেখানোর প্লাগইন - কোডিং

কিউআর কোড জেনারেট করার জন্য আমাদের একটি সার্ভিস ব্যবহার করতে হবে। goqr.me/qr-codes/type-qr-url.html - এই ওয়েবসাইটে এসে "Valid examples" লেখার নিচে এসে প্রথম কিউআর ইমেজের উপরে ক্লিক করব। ইমেজটি ওপেন হলে, এর ইউআরএল টি কপি করে নেই। ইউআরএল এর "data=" অংশের পরে যে লিংক দিব সেটা আমাদের প্লাগিন এ ভ্যারিয়েবল হিসাবে ব্যবহার।

ওয়ার্ডপ্রেস প্লাগিন এর ডিক্লারেশন লিখব নিচের মত করে। তারপর, plugins_loaded হুকের মাধ্যমে textdomain লোড করব। এরপর, the_content ফিল্টার ব্যবহার করে কিউআর কোড এই ফিল্টারের $content ভ্যারিয়েবলের মধ্যে ইনজেক্ট করে দিব।

```
<?php
/*
Plugin Name: Posts to QR Code
Plugin URI: https://www.hellobanglaworld.com/
Description: Creates and display QR Code of the current post
Version: 1.0
Author: Manzur Ahmed
Author URI: https://www.github.com/manzurahmed
License: GPLv2 or later
Text Domain: posts-to-qrcode
Domain Path: /languages/
*/
function qrcode_load_textdomain() {
	load_plugin_textdomain( "posts-to-qrcode", false, dirname(__FILE__)."/languages" );
}
add_action( "plugins_loaded" , "qrcode_load_textdomain" );


function pqrc_display_qrcode( $content ) {
    $current_post_id = get_the_ID();
    $current_post_title = get_the_title( $current_post_id );
    $current_post_url = urlencode( get_the_permalink( $current_post_id ) );
    $image_src = sprintf( "https://api.qrserver.com/v1/create-qr-code/?size=185x185&ecc=L&qzone=1&data=%s",
        $current_post_url );

    $content .= sprintf( '<div class="qrcode"><img src="%s" alt="" /></div>', $image_src, $current_post_title );

    return $content;
}
add_filter( 'the_content', 'pqrc_display_qrcode' );
```

