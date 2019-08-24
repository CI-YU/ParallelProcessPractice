# Parallel Process Practices

�ؼ�: �Хγ̤֪��ɶ�, ���� 1000 �� MyTask instances ���T�ӨB�J�C
����: �C�� MyTask ���P�@�ӨB�J�A���L�k�֦�B�z�C

# �@�k

1. �ЫإߧA�ۤv�� Console App, �Ѧ� ParallelProcessPractice.Core �M��
1. �إߧA�ۤv���l�����O: 
```csharp

public class AndrewTaskRunner : TaskRunnerBase
{
    public override void Run(IEnumerable<MyTask> tasks)
    {
        foreach (var t in tasks)
        {
            t.DoStep1();
            t.DoStep2();
            t.DoStep3();
            Console.WriteLine($"exec job: {t.ID} completed.");
        }
    }
}

```

1. ����A�� test:
```csharp

static void Main(string[] args)
{
    TaskRunnerBase run = new AndrewTaskRunner();
    run.ExecuteTasks(10);
}

```

2. ���浲�G:

```text

Execution Summary: PASS

* Max WIP:
  - ALL:      1
  - Step #1:  1
  - Step #2:  1
  - Step #3:  1

* Execute Time:
  - Time To First Task Completed: 614.3636 msec
  - Time To Last Task Completed:  6047.8443 msec

* Execute Count:
  - Total:   10
  - Success: 10
  - Failure: 0

```

# ����з�:

�u���Ĥ@����� PASS �~��q�L�C��� FAIL �h�Ҧ��ƾڧ����ӻ{�C�ԲӸ�T�i�H�q ```Execute Count``` �o���ԲӪ����\�P���Ѽƶq�C

����Ĳv�|�ѦҨ�ӫ��СA�Ĥ@�u�������ЬO WIP, �ĤG�u�������ЬO ```Execute Time```�C

* �b���~ WIP (Work In Progress):  
> �N��P�ɶ��b�B�z���� Task �`�ơC�B�z���� Task �|�ݭn�ӥθ귽 (�O����A��Ʈw�s�u�ACPU ����)�A�]���n�� code ���৴������ WIP ���ƶq�A�Ʀr�V�ֶV�n�C

* ����ɶ� (Execute Time):  
> �N������O���`�ɶ��A�V�ֶV�n�C

���аѦҪ��u������ (�Ѱ���C):

1. WIP (ALL)
1. Execute Time (FIRST TASK)
1. Execute Time (TOTAL TASK)
1. WIP (STEP1)
1. WIP (STEP2)
1. WIP (STEP3)

# Task �S�x�]�w

�o�ӽm�߱M�ת� Task ���X�ӫe��, �Ҧp�`�@�� 3 �ӨB�J (Step), �C�ӨB�J���J�w������ɶ� (Duration), �]���æ�B�z������C
�o�ǳ]�w�������b PracticeSettings.cs ���C�Y�ݭn�վ�պA�Ӽ������P���ҡA�i�H�����վ�:

```csharp

internal static class PracticeSettings
{
    public const int TASK_TOTAL_STEPS = 3;

    public static readonly int[] TASK_STEPS_DURATION =
    {
        0,  // STEP 0, useless
        300,
        100,
        200
    };

    public static readonly int[] TASK_STEPS_CONCURRENT_LIMIT =
    {
        0,  // STEP 0, useless
        1,
        1,
        1
    };
}

```

�䤤:

* ```TASK_TOTAL_STEPS``` �N���`�@���h�� STEP �ݭn�B�z�C
* ```TASK_STEPS_DURATION``` �N��C�� STEP �B�z�ݭn��O���ɶ� (msec), ���F²�Ƴ]�p, �o�Ӱ}�C�]�w�� indexer �O�q 1 �}�l�C�]���Ĥ@�� [0] �|�Q�������p�C
* ```TASK_STEPS_CONCURRENT_LIMIT``` �N��C�� STEP ���\�̤j�P�ɳB�z���ƶq�C�P�W, �Ĥ@�� [0] �|�Q�������p�C