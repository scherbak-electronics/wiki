# PHP interview
Questions and test tasks during interviews for the PHP developer position.


```php
<?php

class Xxx
{

    private string $fileName;

    public function __construct($fileName)
    {
        $this->fileName = $fileName;
    }

    public function readLine($fileHandle): Iterator
    {
        while (!feof($fileHandle)) {
            yield fgets($fileHandle);
        }
    }

    public function yyy(): array
    {
        $a = range('A', 'Z');
        $f = array_fill_keys(range(0,25), 0);

        $fileHandle = fopen($this->fileName, 'r');

        foreach ($this->readLine($fileHandle) as $line) {
            foreach (str_split(strtoupper($line)) as $i => $char) {
                if (!preg_match('/[A-Z]/', $char)) continue;
                $f[array_search($char, $a)]++;
            }
        }

        fclose($fileHandle);

        return $f;
    }

}

echo json_encode((new Xxx(__DIR__ . '/index.html'))->yyy());
```