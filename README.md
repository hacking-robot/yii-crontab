Crontab is an extension for Yii framework that helps to add system cron jobs. It also support Application console commands.

``` php
$cron = new Crontab('my_crontab'); // my_crontab file will store all added jobs

$jobs_obj = $cron->getJobs(); // previous jobs saved in my_crontab

foreach($jobs_obj as $job)
        echo $job->getCommand();

$cron->eraseJobs(); // erase all previous jobs in my_crontab

// Application console job
$cron->addApplicationJob('yiicmd', 'test1', array(), '*');

// to change job values:
$jobs_obj = $cron->getJobs();
$jobs_obj[0]->setParams(array("'datetime'"));
$jobs_obj[0]->setCommandName('test');

// <= adds a job with: * * * * * php /home/user/my_project/www/yiicmd.php test 'datetime'

// add an other job
$job = new CronApplicationJob('yiicmd', 'test', array("'datetime'"), '0', '0'); // run every day
$job->setParams(array("'date'"));
$cron->add($job);

// <= adds a second job with: 0 0 * * * php /home/user/my_project/www/yiicmd.php test 'date'

// add a regular cron job
$cron->addJob('/home/user/myprogram.bin', '0', '0', '*', '*', '1'); // run every monday
$jobs_obj = $cron->getJobs();
echo $jobs_obj[2]->getCommand();

// <= adds a third job with: 0 0 * * 1 /home/user/myprogram.bin 

$cron->removeJob(2); // removes job with offset 2 (last added here)

$cron->saveCronFile(); // save to my_crontab cronfile

$cron->saveToCrontab(); // adds all my_crontab jobs to system (replacing previous my_crontab jobs)
```


``` php
Content of /protected/extensions/crontab/crontabs/my_crontab :
* * * * * php /home/user/my_project/www/yiicmd.php test 'datetime'
0 0 * * * php /home/user/my_project/www/yiicmd.php test 'date'

```


``` php
// commands/TestCommand.php
<?php 
 
class TestCommand extends CConsoleCommand
{
    public function run($args)
    {
        $table1=new table1;
 
        if($args[0] == 'date')
            $table1->date=date('Y-m-d');
        elseif($args[0] == 'datetime')
            $table1->date=date('Y-m-d H:i:s');
 
        $table1->save();
    }
}
 
 
// yiicmd.php
<?php
 
// change the following paths if necessary
$yii=dirname(__FILE__).'/../yii.php';
$config=dirname(__FILE__).'/protected/config/console.php';
 
// remove the following line when in production mode
defined('YII_DEBUG') or define('YII_DEBUG',true);
 
require_once($yii);
Yii::createConsoleApplication($config)->run();

```


``` php
// protected/config/console.php
<?php

// This is the configuration for yiic console application.
// Any writable CConsoleApplication properties can be configured here.
return array(
        'basePath'=>dirname(__FILE__).DIRECTORY_SEPARATOR.'..',
        'name'=>'My Console Application',

        'import'=>array(
                'application.models.*',
        ),

        // application components
        'components'=>array(
                'db'=>array(
                 'class'=>'CDbConnection',
                 'charset' => 'utf8',
                 'connectionString'=>'mysql:host=localhost;dbname=mydb',
                 'username'=>'root',
                 'password'=>''
                ),
                // usefull for generating links in email etc...
                'urlManager'=>array(
                        'urlFormat'=>'path',
                        'showScriptName' => FALSE,
                        'rules'=>array(),
                ),                      
        ),
);

```
