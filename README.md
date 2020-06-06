# Instagram-scrapper
Scrapp Instagram user data in php


    public function GetTwitterUserStat($username){
       
        $oauth_access_token         = "YOUR_ACCESS_TOKEN";
        $oauth_access_token_secret  = "YOUR_ACCESS_TOKEN_SECRET";
        $consumer_key               = "YOUR_CONSUMER_KEY";
        $consumer_secret            = "YOUR_CONSUMER_SECRET";

        $twitter_timeline           = "user_timeline";  //  mentions_timeline / user_timeline / home_timeline / retweets_of_me

        //  create request
        $request = array(
            'screen_name'       => $username,
            'count'             => '3'
        );

        $oauth = array(
            'oauth_consumer_key'        => $consumer_key,
            'oauth_nonce'               => time(),
            'oauth_signature_method'    => 'HMAC-SHA1',
            'oauth_token'               => $oauth_access_token,
            'oauth_timestamp'           => time(),
            'oauth_version'             => '1.0'
        );

        //  merge request and oauth to one array
        $oauth = array_merge($oauth, $request);

        //  do some magic

        $base_info              = $this->buildBaseString("https://api.twitter.com/1.1/lists/list.json", 'GET', $oauth);
        $composite_key          = rawurlencode($consumer_secret) . '&' . rawurlencode($oauth_access_token_secret);
        $oauth_signature            = base64_encode(hash_hmac('sha1', $base_info, $composite_key, true));
        $oauth['oauth_signature']   = $oauth_signature;

        //  make request
        $header = array($this->buildAuthorizationHeader($oauth), 'Expect:');
        $options = array( CURLOPT_HTTPHEADER => $header,
            CURLOPT_HEADER => false,
            CURLOPT_URL => "https://api.twitter.com/1.1/lists/list.json?". http_build_query($request),
            CURLOPT_RETURNTRANSFER => true,
            CURLOPT_SSL_VERIFYPEER => false);

        $feed = curl_init();
        curl_setopt_array($feed, $options);
        $json = curl_exec($feed);
        curl_close($feed);

        return json_decode($json, true);
    }
