git checkout -b t-lab04
git add -A
git commit -m 't-lab04'
git push --set-upstream origin t-lab04

---

finish ts testing - form testing? - redux testing?

qa and create branches filling in test output
translate to js

git checkout lab03 -f
git clean -df
git push --set-upstream origin lab03

git checkout lab04 -f
git clean -df
git push --set-upstream origin lab04

git checkout lab05 -f
git clean -df
git push --set-upstream origin lab05

//TODO: Do I need to install TS Lint to get linting working?
https://medium.com/@rossbulat/how-to-use-typescript-with-react-and-redux-a118b1e02b76

export function deleteProject(
project: Project
): ThunkAction<void, ProjectState, null, Action<string>> {
return (dispatch: any) => {
if (!project.id) return;
dispatch({ type: DELETE_PROJECT_REQUEST });
return projectAPI
.delete(project.id)
.then(data => {
dispatch({ type: DELETE_PROJECT_SUCCESS, payload: project });
})
.catch(error => {
dispatch({ type: DELETE_PROJECT_FAILURE, payload: error });
});
};

removeProject(project){ - - -
this.props.onRemove(project)
}
