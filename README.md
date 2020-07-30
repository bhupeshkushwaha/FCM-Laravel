# FCM-Laravel

```
<?php

/*
 * To change this license header, choose License Headers in Project Properties.
 * To change this template file, choose Tools | Templates
 * and open the template in the editor.
 */

namespace App\Utility;

/**
 * Description of FcmService
 *
 * @author Bhupesh
 */
class FcmService {

    const FCM_URL = 'https://fcm.googleapis.com/fcm/send';
    const FCM_KEY = "YOUR_FCM_SERVER_KEY";
    const TOKEN_MULTIPLE = "multiple";
    const TOKEN_SINGLE = "single";
    const FCM_TITLE = "Product";
    const FCM_DESCRIPTION = "New products are added";
    const FCM_EXPITY = 14400;

    public function send($title, $body, $type, $token) {
        $notification = array('title' => $title, 'body' => $body, 'sound' => 'default', 'badge' => '1');

        $arrayToSend = array(
            'notification' => $notification,
            'priority' => 'high',
            'timeToLive' => self::FCM_EXPITY,
            "android" => ['ttl' => self::FCM_EXPITY . "s"],
            'apns' => [
                'headers' => [
                    'apns-priority' => "10"
                ]
            ],
            'webpush' => [
                'headers' => [
                    'TTL' => self::FCM_EXPITY
                ]
            ],
        );

        if ($type == self::TOKEN_SINGLE) {
            $arrayToSend['to'] = $token;
        }

        if ($type == self::TOKEN_MULTIPLE) {
            $arrayToSend['registration_ids'] = $token;
        }

        $data = json_encode($arrayToSend);
        //FCM API end-point
        $url = self::FCM_URL;
        //api_key in Firebase Console -> Project Settings -> CLOUD MESSAGING -> Server key
        $server_key = self::FCM_KEY;
        //header with content_type api key
        $headers = array(
            'Content-Type:application/json',
            'Authorization:key=' . $server_key
        );
        //CURL request to route notification to FCM connection server (provided by Google)
        $ch = curl_init();
        curl_setopt($ch, CURLOPT_URL, $url);
        curl_setopt($ch, CURLOPT_POST, true);
        curl_setopt($ch, CURLOPT_HTTPHEADER, $headers);
        curl_setopt($ch, CURLOPT_RETURNTRANSFER, true);
        curl_setopt($ch, CURLOPT_SSL_VERIFYHOST, 0);
        curl_setopt($ch, CURLOPT_SSL_VERIFYPEER, false);
        curl_setopt($ch, CURLOPT_POSTFIELDS, $data);
        $result = curl_exec($ch);
        if ($result === FALSE) {
            return curl_error($ch);

            die('Oops! FCM Send Error: ' . curl_error($ch));
        }
        curl_close($ch);

        return $result;
    }

}

```
