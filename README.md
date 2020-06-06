# Instagram-scrapper
Scrapp Instagram user data in php

 private function getInstagramUserStats($url)
    {
        $regex = '/(?:(?:http|https):\/\/)?(?:www\.)?(?:instagram\.com|instagr\.am)\/([A-Za-z0-9-_\.]+)/im';
        // Verify valid Instagram URL
        if (preg_match($regex, $url, $matches)) {

            $username = $matches[1];
            $response = @file_get_contents( "https://www.instagram.com/$username/?__a=1" );

            if ( $response !== false ) {
                $data = json_decode( $response, true );
                if ( $data !== null ) {

                    $biography=$data['graphql']['user']['biography'];
                    $bussinessName=$data['graphql']['user']['business_category_name'];
                    $follower  = $data['graphql']['user']['edge_followed_by']['count'];
                    $account=['followers'=>$follower,'business_category_name'=>$bussinessName,'biography'=>$biography];
                    return $account;
                }
            }

            return false;
        }

    }
