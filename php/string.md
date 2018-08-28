### 字符串相关

#### 生成随机字符串

```php
<?php
    function randstr($len) {
        $c = "ABCDEFGHIJKLMNOPQRSTUVWXYZabcdefghijklmnopqrstuvwxyz0123456789";
        $ret = "";
        $charlen = strlen($c);
        for($i=0; $i<$len; $i++) { 
            $ret.= $c[rand()%$charlen]; 
        }
        return $ret; 
	}
?>
```

### switch case 弱类型比较
```php
$a = 1;
switch (true) {
    case true === $a:
        echo 'success';
        break;
    default :
        echo 'fail';
}
```

### PHP正确的使用复数 [来源](http://www.cnblogs.com/yingnan/p/5194817.html)
```php
<?php
// 正确地显示复数
if(!function_exists('_plurals_format'))
{
    /**
     * 正确的使用复数    
     * @access   public
     * @author   zhaoyingnan   2016-02-17 11:53
     * @param    string        $sPluralName    非复数形式的名称
     * @param    int           $iAmount        数量
     * @return   string
     * @note    
     **/
    function _plurals_format($sPluralName, $iAmount)
    {
        if(!$sPluralName || !is_numeric($iAmount) || $iAmount <= 0)
            return '';
        // 特殊的复数形式
        $arPluralName = array(
            'addendum'=>'addenda',
            'alga'=>'algae',
            'alumna'=>'alumnae',
            'alumnus'=>'alumni',
            'analysis'=>'analyses',
            'antenna'=>'antennas',//antennae
            'apparatus'=>'apparatuses',
            'appendix'=>'appendices',//appendixes
            'axis'=>'axes',
            'bacillus'=>'bacilli',
            'bacterium'=>'bacteria',
            'basis'=>'bases',
            'beau'=>'beaux',
            'bison'=>'bison',
            'buffalo'=>'buffalos',//buffaloes
            'bureau'=>'bureaus',
            'bus'=>'busses',//buses
            'cactus'=>'cactuses',//cacti
            'calf'=>'calves',
            'child'=>'children',
            'corps'=>'corps',
            'corpus'=>'corpora',//corpuses
            'crisis'=>'crises',
            'criterion'=>'criteria',
            'curriculum'=>'curricula',
            'datum'=>'data',
            'deer'=>'deer',
            'die'=>'dice',
            'dwarf'=>'dwarfs',//dwarves
            'diagnosis'=>'diagnoses',
            'echo'=>'echoes',
            'elf'=>'elves',
            'ellipsis'=>'ellipses',
            'embargo'=>'embargoes',
            'emphasis'=>'emphases',
            'erratum'=>'errata',
            'fireman'=>'firemen',
            'fish'=>'fish',//fishes
            'focus'=>'focuses',
            'foot'=>'feet',
            'formula'=>'formulas',
            'fungus'=>'fungi',//funguses
            'genus'=>'genera',
            'goose'=>'geese',
            'half'=>'halves',
            'hero'=>'heroes',
            'hippopotamus'=>'hippopotami',//hippopotamuses
            'hoof'=>'hoofs',//hooves
            'hypothesis'=>'hypotheses',
            'index'=>'indices',//indexes
            'knife'=>'knives',
            'leaf'=>'leaves',
            'life'=>'lives',
            'loaf'=>'loaves',
            'louse'=>'lice',
            'man'=>'men',
            'matrix'=>'matrices',
            'means'=>'means',
            'medium'=>'media',
            'memorandum'=>'memoranda',
            'millennium'=>'millenniums',//milennia
            'moose'=>'moose',
            'mosquito'=>'mosquitoes',
            'mouse'=>'mice',
            'nebula'=>'nebulae',//nebulas
            'neurosis'=>'neuroses',
            'nucleus'=>'nuclei',
            'oasis'=>'oases',
            'octopus'=>'octopi',//octopuses
            'ovum'=>'ova',
            'ox'=>'oxen',
            'paralysis'=>'paralyses',
            'parenthesis'=>'parentheses',
            'person'=>'people',
            'phenomenon'=>'phenomena',
            'potato'=>'potatoes',
            'radius'=>'radii',//radiuses
            'scarf'=>'scarfs',//scarves
            'self'=>'selves',
            'series'=>'series',
            'sheep'=>'sheep',
            'shelf'=>'shelves',
            'scissors'=>'scissors',
            'species'=>'species',
            'stimulus'=>'stimuli',
            'stratum'=>'strata',
            'syllabus'=>'syllabi',//syllabuses
            'symposium'=>'symposia',//symposiums
            'synthesis'=>'syntheses',
            'synopsis'=>'synopses',
            'tableau'=>'tableaux',
            'that'=>'those',
            'thesis'=>'theses',
            'thief'=>'thieves',
            'this'=>'these',
            'tomato'=>'tomatoes',
            'tooth'=>'teeth',
            'torpedo'=>'torpedoes',
            'vertebra'=>'vertebrae',
            'veto'=>'vetoes',
            'vita'=>'vitae',
            'watch'=>'watches',
            'wife'=>'wives',
            'wolf'=>'wolves',
            'woman'=>'women',
            'zero'=>'zeros',//zeroes
        );

        // 如果只有一个
        if($iAmount == 1)
            return $sPluralName;

        //　如果超过一个，并且是特殊的复数形式
        if(isset($arPluralName[$sPluralName]))
            return $arPluralName[$sPluralName];

        // 超过一个，并且是一个标准的复数形式
        return $sPluralName.'s';
    }
}

echo 'Sybil ate three '._plurals_format('biscuit', 3).', one after the other.',PHP_EOL;
echo 'The two '._plurals_format('woman', 2).' will meet tomorrow in the final.';
```

### 判断字符编码
```php
function get_encoding($str = ""){
        $encodings = array(
            'ASCII',
            'UTF-8',
            'GBK'
        );
        foreach ( $encodings as $encoding ) {
            if ($str === mb_convert_encoding(mb_convert_encoding($str, "UTF-32", $encoding), $encoding, "UTF-32")) {
                return $encoding;
            }
        }
        return false;
    }
```
