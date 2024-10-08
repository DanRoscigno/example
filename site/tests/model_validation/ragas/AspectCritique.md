# AspectCritique

Evaluates generations against the following aspects: harmfulness, maliciousness,
coherence, correctness, and conciseness.

### Overview:

This is designed to assess submissions against predefined and user-defined "aspects".
For each aspect, a judge LLM is prompted to critique a piece of generated text based
on a description of the aspect. The output of this evaluation is a binary (0/1 = yes/no)
score that indicates whether the submission aligns with the defined aspect or not.

### Inputs and Outputs:

The input to this metric is a dataset containing the input `question` (prompt to the LLM)
and the `answer` (text generated by the LLM). Any retrieved `contexts` can also be
included to enhance the evaluation.

The `question_column`, `answer_column`, and `contexts_column` parameters can be used to
specify the names or sources for the data that this metric will evaluate if the dataset
does not contain the required columns `question`, `answer`, and `contexts`.

By default, the aspects evaluated are harmfulness, maliciousness, coherence,
correctness, and conciseness. To change the aspects evaluated, the `aspects` parameter
can be set to a list containing any of these aspects.

To add custom aspects, the `additional_aspects` parameter can be passed as a list
of tuples where each tuple contains the aspect name and a description of the aspect
that the judge LLM will use to critique the submission.

The output of this metric is a table of scores for each aspect where the aspect score
is the number of "yes" scores divided by the total number of submissions:
$$
\\text{aspect score} = \\frac{\\text{number of "yes" scores}}{\\text{total number of submissions}}
$$

### Examples:

- **Mapping to Required Columns:** If the dataset does not contain the columns required
to run this metric (i.e., `question`, `answer`, and `contexts`), the

```python
pred_col = my_vm_dataset.prediction_column(my_vm_model)
run_test(
validmind.model_validation.ragas.AspectCritique",
inputs={"dataset": my_vm_dataset},
params={
question_column": "input_prompt",
answer_column": f"{pred_col}.llm_output",
contexts_column": lambda row: [row[pred_col]["context_message"]],
},
)
```

- **Custom Aspects:** To evaluate custom aspects, the `additional_aspects` parameter can
be set to a list of tuples where each tuple contains the aspect name and a description
of the aspect that the judge LLM will use to critique the submission. For example, to
evaluate whether the LLM-generated text has a "professional tone", the `additional_aspects`
parameter can be set like this:

```python
run_test(
validmind.model_validation.ragas.AspectCritique",
inputs={"dataset": my_vm_dataset},
params={
additional_aspects": [
("professionalism", "Does the text have a professional tone?"),
],
},
)
```